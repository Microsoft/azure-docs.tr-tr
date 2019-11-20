---
title: Azure Application Gateway ölçümlerini kullanarak AKS 'leri otomatik ölçeklendirme
description: Bu makale, Application Gateway ölçümleri ve Azure Kubernetes ölçüm bağdaştırıcısı kullanarak AKS arka uç yığınlarınızı ölçeklendirmeye yönelik yönergeler sağlar
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 0e1ba6d86778b40f96940c417050e242fde33845
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73797588"
---
# <a name="autoscale-your-aks-pods-using-application-gateway-metrics-beta"></a>Application Gateway ölçümlerini kullanarak AKS yığınlarınızı otomatik ölçeklendirme (Beta)

Gelen trafik arttıkça, talebe göre uygulamalarınızın ölçeğini ölçeklendirmek çok önemli hale gelir.

Aşağıdaki öğreticide, Application Gateway `AvgRequestCountPerHealthyHost` ölçümünü kullanarak uygulamanızı nasıl ölçeklendirebileceğiniz açıklanmıştır. `AvgRequestCountPerHealthyHost` belirli bir arka uç havuzuna ve arka uç HTTP ayarı birleşimine gönderilen ortalama isteği ölçer.

Aşağıdaki iki bileşeni kullanacağız:

* [`Azure Kubernetes Metric Adapter`](https://github.com/Azure/azure-k8s-metrics-adapter) , ölçüm sunucusu üzerinden Application Gateway ölçümlerini göstermek için ölçüm bağdaştırıcısını kullanacağız. Azure Kubernetes ölçüm bağdaştırıcısı, Application Gateway giriş denetleyicisine benzer şekilde Azure 'daki açık kaynaklı bir projem. 
* [`Horizontal Pod Autoscaler`](https://docs.microsoft.com/azure/aks/concepts-scale#horizontal-pod-autoscaler) , hpa 'yi Application Gateway ölçümleri kullanacak ve ölçeklendirmeye yönelik bir dağıtımı hedefleyecek şekilde kullanacağız.

## <a name="setting-up-azure-kubernetes-metric-adapter"></a>Azure Kubernetes ölçüm bağdaştırıcısını ayarlama

1. İlk olarak bir Azure AAD hizmet sorumlusu oluşturacak ve Application Gateway kaynak grubu üzerinden erişim `Monitoring Reader` atayacağız. 

    ```bash
        applicationGatewayGroupName="<application-gateway-group-id>"
        applicationGatewayGroupId=$(az group show -g $applicationGatewayGroupName -o tsv --query "id")
        az ad sp create-for-rbac -n "azure-k8s-metric-adapter-sp" --role "Monitoring Reader" --scopes applicationGatewayGroupId
    ```

1. Şimdi, yukarıda oluşturulan AAD hizmet sorumlusunu kullanarak [`Azure Kubernetes Metric Adapter`](https://github.com/Azure/azure-k8s-metrics-adapter) dağıtacağız.

    ```bash
    kubectl create namespace custom-metrics
    # use values from service principle created above to create secret
    kubectl create secret generic azure-k8s-metrics-adapter -n custom-metrics \
        --from-literal=azure-tenant-id=<tenantid> \
        --from-literal=azure-client-id=<clientid> \
        --from-literal=azure-client-secret=<secret>
    kubectl apply -f kubectl apply -f https://raw.githubusercontent.com/Azure/azure-k8s-metrics-adapter/master/deploy/adapter.yaml -n custom-metrics
    ```

1. `appgw-request-count-metric`adında bir `ExternalMetric` kaynağı oluşturacağız. Bu kaynak, ölçüm bağdaştırıcısının `myResourceGroup` kaynak grubundaki `myApplicationGateway` kaynak için `AvgRequestCountPerHealthyHost` ölçümünü kullanıma sunmasını sağlar. Application Gateway belirli bir arka uç havuzunu ve arka uç HTTP ayarını hedeflemek için `filter` alanını kullanabilirsiniz.

    ```yaml
    apiVersion: azure.com/v1alpha2
    kind: ExternalMetric
    metadata:
    name: appgw-request-count-metric
    spec:
        type: azuremonitor
        azure:
            resourceGroup: myResourceGroup # replace with your application gateway's resource group name
            resourceName: myApplicationGateway # replace with your application gateway's name
            resourceProviderNamespace: Microsoft.Network
            resourceType: applicationGateways
        metric:
            metricName: AvgRequestCountPerHealthyHost
            aggregation: Average
            filter: BackendSettingsPool eq '<backend-pool-name>~<backend-http-setting-name>' # optional
    ```

Artık yeni ölçümünüzün sunulduğunu görmek için ölçüm sunucusuna bir istek yapabilirsiniz:
```bash
kubectl get --raw "/apis/external.metrics.k8s.io/v1beta1/namespaces/default/appgw-request-count-metric"
# Sample Output
# {
#   "kind": "ExternalMetricValueList",
#   "apiVersion": "external.metrics.k8s.io/v1beta1",
#   "metadata":
#     {
#       "selfLink": "/apis/external.metrics.k8s.io/v1beta1/namespaces/default/appgw-request-count-metric",
#     },
#   "items":
#     [
#       {
#         "metricName": "appgw-request-count-metric",
#         "metricLabels": null,
#         "timestamp": "2019-11-05T00:18:51Z",
#         "value": "30",
#       },
#     ],
# }
```

## <a name="using-the-new-metric-to-scale-up-the-deployment"></a>Dağıtımı ölçeklendirmek için yeni ölçümü kullanma

Ölçüm sunucusu üzerinden `appgw-request-count-metric` kullanıma sunabilebilmemiz, hedef dağıtımımızın ölçeğini ölçeklendirmek için [`Horizontal Pod Autoscaler`](https://docs.microsoft.com/azure/aks/concepts-scale#horizontal-pod-autoscaler) kullanmaya hazırız.

Aşağıdaki örnekte, örnek bir dağıtım `aspnet`hedefliyoruz. En fazla `10` Pod 'ye kadar Pod başına `appgw-request-count-metric` > 200 ' i ölçeklendireceğiz.

Hedef dağıtım adınızı değiştirin ve aşağıdaki otomatik ölçek yapılandırmasını uygulayın:
```yaml
apiVersion: autoscaling/v2beta1
kind: HorizontalPodAutoscaler
metadata:
  name: deployment-scaler
spec:
  scaleTargetRef:
    apiVersion: extensions/v1beta1
    kind: Deployment
    name: aspnet # replace with your deployment's name
  minReplicas: 1
  maxReplicas: 10
  metrics:
  - type: External
    external:
      metricName: appgw-request-count-metric
      targetAverageValue: 200
```

Apache tezgahtır gibi bir yük testi aracı kullanarak kurulumunuzu test edin:
```bash
ab -n10000 http://<applicaiton-gateway-ip-address>/
```

## <a name="next-steps"></a>Sonraki adımlar
- Giriş [**denetleyicisi sorunlarını giderme**](ingress-controller-troubleshoot.md): giriş denetleyicisiyle ilgili sorunları giderin.