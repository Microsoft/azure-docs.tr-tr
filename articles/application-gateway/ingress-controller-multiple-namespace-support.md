---
title: Application Gateway giriş denetleyicisi için birden çok ad alanı desteği etkinleştir
description: Bu makalede, bir Kubernetes kümesinde Application Gateway giriş denetleyicisi ile birden çok ad alanı desteğinin nasıl etkinleştirileceği hakkında bilgi verilmektedir.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: c13c4410852d97f0bf4548578f40a5cc560804d7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94874602"
---
# <a name="enable-multiple-namespace-support-in-an-aks-cluster-with-application-gateway-ingress-controller"></a>Application Gateway Ingress denetleyicisi ile bir AKS kümesinde birden çok ad alanı desteğini etkinleştirme

## <a name="motivation"></a>Motivasyon
Kubernetes [ad alanları](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/) , bir Kubernetes kümesinin bölümlenmesi ve daha büyük bir ekibin alt grupları için ayrılmasını mümkün kılar. Daha sonra bu alt takımlar daha ayrıntılı kaynak, güvenlik, yapılandırma vb. denetimlerle altyapıyı dağıtabilir ve yönetebilir. Kubernetes, bir veya daha fazla giriş kaynaklarının her bir ad alanı içinde bağımsız olarak tanımlanmasını sağlar.

Sürüm 0,7 itibariyle [Azure Application Gateway Kubernetes IngressController](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/master/README.md) (AGIC), içindeki olayları alabilir ve birden çok ad alanını gözlemleyebilirsiniz. AKS Yöneticisi, [uygulama ağ geçidini](https://azure.microsoft.com/services/application-gateway/) bir giriş olarak kullanmaya karar verirse, tüm ad alanları Application Gateway aynı örneğini kullanır. Tek bir giriş denetleyicisi yüklemesi, erişilebilir ad alanlarını izleyecek ve ilişkilendirildiği Application Gateway yapılandıracaktır.

Bağımsız `default` olarak, Helm yapılandırmasındaki bir veya daha fazla farklı ad alanına açıkça değiştirilmediği sürece AGIC sürüm 0,7, ad alanını özel olarak gözlemlemeye devam eder (aşağıdaki bölümüne bakın).

## <a name="enable-multiple-namespace-support"></a>Birden çok ad alanı desteğini etkinleştirme
Birden çok ad alanı desteğini etkinleştirmek için:
1. [Helm-config. YAML](#sample-helm-config-file) dosyasını aşağıdaki yollarla değiştirin:
   - `watchNamespace`anahtarı tamamen [helk-config konumundan silin. YAML](#sample-helm-config-file) -agic tüm ad alanlarını gözlemleyecek
   - `watchNamespace`boş bir dizeye ayarla-AGIC, tüm ad alanlarını gözlemleyecek
   - virgülle ayrılmış birden çok ad alanı ekleme ( `watchNamespace: default,secondNamespace` )-agic bu ad alanlarını özel olarak gözlemleyecek
2. ile Helmtemplate değişikliklerini Uygula: `helm install -f helm-config.yaml application-gateway-kubernetes-ingress/ingress-azure`

Birden çok ad alanını gözlemleyebilme özelliği ile dağıtıldıktan sonra, AGIC şunları sağlar:
  - tüm erişilebilir ad alanlarından giriş kaynaklarını listeleyin
  - giriş kaynakları için açıklama eklenmiş olarak filtrele `kubernetes.io/ingress.class: azure/application-gateway`
  - Birleşik [Application Gateway yapılandırması](https://github.com/Azure/azure-sdk-for-go/blob/37f3f4162dfce955ef5225ead57216cf8c1b2c70/services/network/mgmt/2016-06-01/network/models.go#L1710-L1744) oluştur
  - yapılandırmayı [ARM](../azure-resource-manager/management/overview.md) aracılığıyla ilişkili Application Gateway uygulama

## <a name="conflicting-configurations"></a>Çakışan yapılandırma
Birden çok gösterilemez [ınress kaynağı](https://kubernetes.io/docs/concepts/services-networking/ingress/#the-ingress-resource) , tek bir Application Gateway için çakışan yapılandırmaların oluşturulmasını sağlayabilir. (Örnek için aynı etki alanını talep eden iki giriş.)

Hiyerarşinin en **üstünde (IP** adresi, bağlantı noktası ve ana bilgisayar) ve **yönlendirme kuralları** (bağlama dinleyicisi, arka uç havuzu ve http ayarları), birden çok ad alanı/giriş tarafından oluşturulup paylaşılabilir.

Diğer yandan yollar, arka uç havuzları, HTTP ayarları ve TLS sertifikaları yalnızca bir ad alanı tarafından oluşturulabilir ve yinelemeler kaldırılır.

Örneğin, aşağıdaki yinelenen giriş kaynakları tanımlı ad alanlarını ve için göz önünde bulundurun `staging` `production` `www.contoso.com` :

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

İki giriş kaynağına, ilgili `www.contoso.com` Kubernetes ad alanlarına yönlendirilmek için trafik talep etmekte olsa da, trafiğe yalnızca bir arka uç hizmet verebilir. AGIC, kaynaklardan biri için "ilk olarak ilk kez sunulan" temelinde bir yapılandırma oluşturur. Aynı anda iki giriş kaynağı oluşturulursa, alfabede daha önceki bir işlem öncelikli olur. Yukarıdaki örnekte, yalnızca giriş için ayarları oluşturabileceksiniz `production` . Application Gateway, aşağıdaki kaynaklarla yapılandırılacak:

  - Oluşturulurken `fl-www.contoso.com-80`
  - Yönlendirme kuralı: `rr-www.contoso.com-80`
  - Arka uç havuzu: `pool-production-contoso-web-service-80-bp-80`
  - HTTP ayarları: `bp-production-contoso-web-service-80-80-websocket-ingress`
  - Durum araştırması: `pb-production-contoso-web-service-80-websocket-ingress`

*Dinleyici* ve *yönlendirme kuralı* haricinde oluşturulan Application Gateway kaynakları, oluşturuldukları ad alanının () adını içerir `production` .

İki giriş kaynağı, zaman içinde farklı noktalarda AKS kümesine tanıtılıyorsa, AGC 'nin Application Gateway yeniden yapılandırdığı ve trafiği ' den ' a yeniden yönlendirdiği bir senaryoya kadar sona erdirmek olasıdır `namespace-B` `namespace-A` .

Örneğin `staging` , önce eklediyseniz, AGIC, trafiği hazırlama arka uç havuzuna yönlendirmek için Application Gateway yapılandırır. Daha sonraki bir aşamada, giriş girişi `production` , `production` arka uç havuzuna trafiği yönlendirmeyi Başlatan Application Gateway yeniden programmasına neden olur.

## <a name="restrict-access-to-namespaces"></a>Ad alanlarına erişimi kısıtlama
Varsayılan olarak AGIC, Application Gateway herhangi bir ad alanı içinde açıklamalı giriş temelli olarak yapılandırır. Bu davranışı sınırlandırmak istiyorsanız aşağıdaki seçeneklere sahip olursunuz:
  - ad alanlarını sınırsız olarak tanımlayarak ad alanlarını sınırlayın, `watchNamespace` [helk-config. YAML](#sample-helm-config-file) içindeki YAML anahtarı aracılığıyla gözlemelidir
  - AGIC 'i belirli ad alanlarıyla sınırlamak için [rol/RoleBinding](../aks/azure-ad-rbac.md) kullanın

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
    # Specify if the cluster is Kubernetes RBAC enabled or not
    rbac:
        enabled: false # true/false
    
    # Specify aks cluster related information. THIS IS BEING DEPRECATED.
    aksClusterConfiguration:
        apiServerAddress: <aks-api-server-address>
```