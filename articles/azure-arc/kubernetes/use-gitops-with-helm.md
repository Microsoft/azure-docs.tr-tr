---
title: Yay etkin Kubernetes kümesinde giler kullanarak Held grafiklerini dağıtma
services: azure-arc
ms.service: azure-arc
ms.date: 03/03/2021
ms.topic: article
author: mlearned
ms.author: mlearned
description: Azure Arc etkin küme yapılandırması için Held ile Gilar kullanma
keywords: Gilar, Kubernetes, K8s, Azure, Held, Arc, AKS, Azure Kubernetes hizmeti, kapsayıcılar
ms.openlocfilehash: df9b40764ec463553659803749f282bbc4587bde
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449552"
---
# <a name="deploy-helm-charts-using-gitops-on-an-arc-enabled-kubernetes-cluster"></a>Bir yay etkin bir Kubernetes kümesinde Gilar kullanarak Held grafikleri dağıtma

Helm, Kubernetes uygulamalarını yüklemenize ve yaşam döngüsünü yönetmenize yardımcı olan bir açık kaynak paketleme aracıdır. APT ve yum gibi Linux paket yöneticilerine benzer şekilde, Helm, önceden yapılandırılmış Kubernetes kaynakları paketleri olan Kubernetes grafiklerini yönetmek için kullanılır.

Bu makalede, Azure Arc etkinleştirilmiş Kubernetes ile Held 'yi yapılandırma ve kullanma işlemlerinin nasıl yapılacağı gösterilir.

## <a name="prerequisites"></a>Önkoşullar

- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Mevcut bir Azure Arc, Kubernetes bağlı kümesini etkinleştirdi.
    - Henüz bir küme bağlanamadıysanız, [Azure Arc özellikli bir Kubernetes kümesi hızlı başlangıç](quickstart-connect-cluster.md)' i inceleyin.
- Bu özelliğin avantajları ve mimarisi hakkında bilgiler. Yapılandırmalarda daha fazla bilgi edinin [ve Azure Arc etkin Kubernetes makalesini](conceptual-configurations.md)okuyun.
- `k8s-configuration`Sürüm >= 1.0.0 Azure CLI uzantısını yükler:
  
  ```azurecli
  az extension add --name k8s-configuration
  ```

## <a name="overview-of-using-gitops-and-helm-with-azure-arc-enabled-kubernetes"></a>Azure Arc etkin Kubernetes ile Gira ve Held kullanımına genel bakış

 Hele işleci, Held grafik sürümlerini otomatikleştiren bir Flox uzantısı sağlar. Helk grafik sürümü, HelmRelease adlı bir Kubernetes özel kaynağı aracılığıyla açıklanır. Flox, bu kaynakları git 'ten kümeye eşitler, ancak Held operatörü kaynaklarda belirtilen şekilde Heln grafiklerinin serbest bırakılacağını sağlar.

 Bu makalede kullanılan [örnek depo](https://github.com/Azure/arc-helm-demo) aşağıdaki şekilde yapılandırılmıştır:

```console
├── charts
│   └── azure-arc-sample
│       ├── Chart.yaml
│       ├── templates
│       │   ├── NOTES.txt
│       │   ├── deployment.yaml
│       │   └── service.yaml
│       └── values.yaml
└── releases
    └── app.yaml
```

Git deposunda iki dizin vardır: biri bir helk grafiği ve biri de yayınlar yapılandırmasını içerir. Dizininde, `releases` `app.yaml` aşağıda gösterildiği HelmRelease yapılandırmasını içerir:

```yaml
apiVersion: helm.fluxcd.io/v1
kind: HelmRelease
metadata:
  name: azure-arc-sample
  namespace: arc-k8s-demo
spec:
  releaseName: arc-k8s-demo
  chart:
    git: https://github.com/Azure/arc-helm-demo
    ref: master
    path: charts/azure-arc-sample
  values:
    serviceName: arc-k8s-demo
```

HELI yayın yapılandırması aşağıdaki alanları içerir:

| Alan | Açıklama |
| ------------- | ------------- | 
| `metadata.name` | Zorunlu alan. Kubernetes adlandırma kurallarını izlemesi gerekir. |
| `metadata.namespace` | İsteğe bağlı alan. Sürümün oluşturulduğu yeri belirler. |
| `spec.releaseName` | İsteğe bağlı alan. Sağlanmazsa, yayın adı olacaktır `$namespace-$name` . |
| `spec.chart.path` | Grafiği içeren dizin (depo köküne göre). |
| `spec.values` | Varsayılan parametre değerlerinin grafiğin kendisindeki Kullanıcı özelleştirmeleri. |

HelmRelease içinde belirtilen seçenekler, `spec.values` grafik kaynağından belirtilen seçenekleri geçersiz kılar `values.yaml` .

HelmRelease hakkında daha fazla bilgi için resmi [hele işleci belgelerine bakın](https://docs.fluxcd.io/projects/helm-operator/en/stable/).

## <a name="create-a-configuration"></a>Yapılandırma oluşturma

İçin Azure CLı uzantısını kullanarak `k8s-configuration` , bağlı kümenizi örnek git deposuna bağlayın. Bu yapılandırmaya ad verin `azure-arc-sample` ve Flox işlecini `arc-k8s-demo` ad alanına dağıtın.

```console
az k8s-configuration create --name azure-arc-sample --cluster-name AzureArcTest1 --resource-group AzureArcTest --operator-instance-name flux --operator-namespace arc-k8s-demo --operator-params='--git-readonly --git-path=releases' --enable-helm-operator --helm-operator-chart-version='1.2.0' --helm-operator-params='--set helm.versions=v3' --repository-url https://github.com/Azure/arc-helm-demo.git --scope namespace --cluster-type connectedClusters
```

### <a name="configuration-parameters"></a>Yapılandırma parametreleri

Yapılandırmanın oluşturulmasını özelleştirmek için [ek parametreler hakkında bilgi edinin](./tutorial-use-gitops-connected-cluster.md#additional-parameters).

## <a name="validate-the-configuration"></a>Yapılandırmayı doğrulama

Azure CLı 'yı kullanarak yapılandırmanın başarıyla oluşturulduğunu doğrulayın.

```console
az k8s-configuration show --name azure-arc-sample --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

Yapılandırma kaynağı uyumluluk durumu, iletiler ve hata ayıklama bilgileriyle güncelleştirilir.

```output
{
  "complianceStatus": {
    "complianceState": "Installed",
    "lastConfigApplied": "2019-12-05T05:34:41.481000",
    "message": "{\"OperatorMessage\":null,\"ClusterState\":null}",
    "messageLevel": "3"
  },
  "enableHelmOperator": "True",
  "helmOperatorProperties": {
    "chartValues": "--set helm.versions=v3",
    "chartVersion": "1.2.0"
  },
  "id": "/subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1/providers/Microsoft.KubernetesConfiguration/sourceControlConfigurations/azure-arc-sample",
  "name": "azure-arc-sample",
  "operatorInstanceName": "flux",
  "operatorNamespace": "arc-k8s-demo",
  "operatorParams": "--git-readonly --git-path=releases",
  "operatorScope": "namespace",
  "operatorType": "Flux",
  "provisioningState": "Succeeded",
  "repositoryPublicKey": "",
  "repositoryUrl": "https://github.com/Azure/arc-helm-demo.git",
  "resourceGroup": "AzureArcTest",
  "type": "Microsoft.KubernetesConfiguration/sourceControlConfigurations"
}
```

## <a name="validate-application"></a>Uygulamayı doğrula

Aşağıdaki komutu çalıştırın ve `localhost:8080` uygulamanın çalıştığını doğrulamak için tarayıcınızda öğesine gidin.

```console
kubectl port-forward -n arc-k8s-demo svc/arc-k8s-demo 8080:8080
```

## <a name="next-steps"></a>Sonraki adımlar

[Azure ilkesi](./use-azure-policy.md)'ni kullanarak ölçek üzerinde küme yapılandırması uygulayın.
