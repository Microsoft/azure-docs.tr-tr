---
title: Azure Kubernetes hizmeti (AKS) denetleyici günlüklerini görüntüleme
description: Azure Kubernetes Service (AKS) ' de Kubernetes denetim düzlemi için günlükleri etkinleştirme ve görüntüleme hakkında bilgi edinin
services: container-service
ms.topic: article
ms.date: 01/27/2020
ms.openlocfilehash: 395422ca97b43488a7d7ad1c7434b20ccc59f2b0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767300"
---
# <a name="enable-and-review-kubernetes-control-plane-logs-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service 'te (AKS) Kubernetes denetim düzlemi günlüklerini etkinleştirme ve gözden geçirme

Azure Kubernetes Service (AKS) ile, *kuas-apiserver* ve *kuin-Controller-Manager* gibi denetim düzlemi bileşenleri yönetilen bir hizmet olarak sağlanır. *Kubelet* ve kapsayıcı çalışma zamanı 'nı çalıştıran düğümleri oluşturup yönetirsiniz ve uygulamalarınızı yönetilen Kubernetes API sunucusu aracılığıyla dağıtırsınız. Uygulamanızın ve hizmetlerinizin sorunlarını gidermenize yardımcı olmak için, bu denetim düzlemi bileşenleri tarafından oluşturulan günlükleri görüntülemeniz gerekebilir. Bu makalede, Kubernetes denetim düzlemi bileşenlerinden günlükleri etkinleştirmek ve sorgulamak için Azure Izleyici günlüklerinin nasıl kullanılacağı gösterilir.

## <a name="before-you-begin"></a>Başlamadan önce

Bu makalede, Azure hesabınızda çalışan mevcut bir AKS kümesi gerekir. Zaten bir AKS kümeniz yoksa, [Azure CLI][cli-quickstart] veya [Azure Portal][portal-quickstart]kullanarak bir tane oluşturun. Azure Izleyici günlükleri hem Kubernetes RBAC, Azure RBAC hem de RBAC özellikli olmayan AKS kümeleriyle birlikte çalışmaktadır.

## <a name="enable-resource-logs"></a>Kaynak günlüklerini etkinleştirme

Azure Izleyici günlükleri, birden fazla kaynaktaki verileri toplayıp gözden geçirmesine yardımcı olmak için ortamınıza ilişkin Öngörüler sağlayan bir sorgu dili ve analiz altyapısı sağlar. Çalışma alanı, verileri harmanlamak ve analiz etmek için kullanılır ve Application Insights ve Güvenlik Merkezi gibi diğer Azure hizmetleriyle tümleştirilebilir. Günlükleri çözümlemek üzere farklı bir platform kullanmak için, kaynak günlüklerini bir Azure depolama hesabına veya Olay Hub 'ına göndermek yerine seçebilirsiniz. Daha fazla bilgi için bkz. [Azure izleyici günlükleri nedir?][log-analytics-overview].

Azure Izleyici günlükleri Azure portal etkinleştirilir ve yönetilir. AKS kümenizdeki Kubernetes denetim düzlemi bileşenleri için günlük toplamayı etkinleştirmek üzere, Azure portal bir Web tarayıcısında açın ve aşağıdaki adımları uygulayın:

1. AıKS kümeniz için kaynak grubunu ( *Myresourcegroup* gibi) seçin. *MC_myResourceGroup_myAKSCluster_eastus* gibi bireysel aks kümesi kaynaklarınızı içeren kaynak grubunu seçmeyin.

2. Sol taraftaki **Tanılama ayarları**' nı seçin.

3. *Myakscluster* gibi aks kümenizi seçin ve ardından **Tanılama ayarı eklemeyi** seçin.
  :::image type="content" source="media\view-control-plane-logs\select-add-diagnostic-setting.PNG" alt-text="' Tanılama ayarlarını Ekle ' öğesinin seçilmesi gerektiğini belirten bir tarayıcı penceresinde Azure portal ekran görüntüsü":::

4. *Myaksclusterlogs* gibi bir ad girin ve **Log Analytics çalışma alanına gönderme** seçeneğini belirleyin.

5. Mevcut bir çalışma alanını seçin veya yenisini oluşturun. Bir çalışma alanı oluşturursanız, bir çalışma alanı adı, bir kaynak grubu ve bir konum sağlayın.

6. Kullanılabilir Günlükler listesinde, etkinleştirmek istediğiniz günlükleri seçin. Bu örnekte, *kuin-Audit* ve *Kuto-Audit-admin* günlüklerini etkinleştirin. Ortak Günlükler *kuin-apiserver*, *kuin-Controller-Manager* ve *kuin-Scheduler*' ı kapsar. Toplama Log Analytics çalışma alanları etkinleştirildikten sonra, toplanan günlükleri döndürebilir ve değiştirebilirsiniz.

7. Hazırsanız, seçili günlüklerin toplanmasını etkinleştirmek için **Kaydet** ' i seçin.
  :::image type="content" source="media\view-control-plane-logs\settings-selected.PNG" alt-text="Azure portal ' tanılama ayarı Ekle ' ekranının ekran görüntüsü. ' ' Log Analytics çalışma alanına Gönder ' hedefi ve ' kuin-Audit ' ve ' kuin-Audit-admin ' günlükleri seçildi":::

## <a name="log-categories"></a>Günlük kategorileri

Kubernetes tarafından yazılan girdilerin yanı sıra, projenizin denetim günlükleri de AKS girdilerini de içermelidir.

Denetim günlükleri üç kategoriye kaydedilir: *kual-Audit*, *kuin-Audit-admin* ve *Guard*.

- *Kuin-Audit* kategorisi, *Get*, *list*, *Create*, *Update*, *Delete*, *Patch* ve *Post* gibi her denetim olayının tüm denetim günlüğü verilerini içerir.
- *Kuin-Audit-admin* kategorisi, *kuin-Audit* günlük kategorisinin bir alt kümesidir. *Kuto-Audit-admin* , günlükteki *Get* ve *list* denetim olaylarını dışlayarak günlük sayısını önemli ölçüde azaltır.
- *Koruma* kategorisi, Azure AD ve Azure RBAC denetimleri ile yönetilir. Yönetilen Azure AD için: içindeki belirteci, Kullanıcı bilgilerini dışarı. Azure RBAC için: içindeki ve giden erişim gözden geçirmeleri.

## <a name="schedule-a-test-pod-on-the-aks-cluster"></a>AKS kümesinde bir test Pod 'u zamanlama

Bazı günlükler oluşturmak için AKS kümenizde yeni bir pod oluşturun. Aşağıdaki örnek YAML bildirimi, temel bir NGıNX örneği oluşturmak için kullanılabilir. Seçtiğiniz düzenleyicide adlı bir dosya oluşturun `nginx.yaml` ve aşağıdaki içeriği yapıştırın:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  nodeSelector:
    "beta.kubernetes.io/os": linux
  containers:
  - name: mypod
    image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    ports:
    - containerPort: 80
```

[Kubectl Create][kubectl-create] komutuyla Pod oluşturun ve aşağıdaki örnekte gösterildiği gibi YAML Dosyanızı belirtin:

```
$ kubectl create -f nginx.yaml

pod/nginx created
```

## <a name="view-collected-logs"></a>Toplanan günlükleri görüntüle

Tanılama günlüklerinin etkinleştirilmesi ve görünmesi 10 dakikaya kadar sürebilir.

> [!NOTE]
> Uyumluluk veya başka amaçlar için tüm denetim günlüğü verilerine ihtiyacınız varsa, bunu toplayın ve BLOB depolama gibi ucuz depolamada saklayın. İzleme ve uyarı amaçlarıyla anlamlı bir denetim günlüğü verileri kümesi toplamak ve kaydetmek için *kuin-Audit-admin* günlük kategorisini kullanın.

Azure portal, AKS kümenize gidin ve sol taraftaki **Günlükler** ' i seçin. Görüntülenirse, *örnek sorgular* penceresini kapatın.

Sol taraftaki **Günlükler**' i seçin. *Kuin-denetim* günlüklerini görüntülemek için metin kutusuna aşağıdaki sorguyu girin:

```
AzureDiagnostics
| where Category == "kube-audit"
| project log_s
```

Büyük olasılıkla çok sayıda günlük döndürülür. Önceki adımda oluşturulan NGıNX Pod hakkındaki günlükleri görüntülemek için sorguyu aşağı bağlamak üzere, aşağıdaki örnek sorguda gösterildiği gibi *NGINX* için arama yapmak üzere ek bir *WHERE* ifadesini ekleyin:

```
AzureDiagnostics
| where Category == "kube-audit"
| where log_s contains "nginx"
| project log_s
```

*Kuin-Audit-admin* günlüklerini görüntülemek için, metin kutusuna aşağıdaki sorguyu girin:

```
AzureDiagnostics
| where Category == "kube-audit-admin"
| project log_s
```

Bu örnekte sorgu, *kuin-Audit-admin* içinde tüm oluşturma işlerini gösterir. Önceki adımda oluşturulan NGıNX Pod ile ilgili günlükleri görüntülemek için sorgunun kapsamını göstermek üzere çok sayıda sonuç döndürüldü, aşağıdaki örnek sorguda gösterildiği gibi *NGINX* için arama yapmak üzere ek bir *WHERE* açıklaması ekleyin.

```
AzureDiagnostics
| where Category == "kube-audit-admin"
| where log_s contains "nginx"
| project log_s
```


Günlük verilerinizi sorgulama ve filtreleme hakkında daha fazla bilgi için bkz. [Log Analytics günlük araması ile toplanan verileri görüntüleme veya çözümleme][analyze-log-analytics].

## <a name="log-event-schema"></a>Olay şemasını günlüğe kaydet

AKS aşağıdaki olayları günlüğe kaydeder:

* [AzureActivity][log-schema-azureactivity]
* [AzureDiagnostics][log-schema-azurediagnostics]
* [AzureMetrics][log-schema-azuremetrics]
* [Containerımageınventory][log-schema-containerimageinventory]
* [Containerınventory][log-schema-containerinventory]
* [ContainerLog][log-schema-containerlog]
* [Containernodeınventory][log-schema-containernodeinventory]
* [ContainerServiceLog][log-schema-containerservicelog]
* [Sinyal][log-schema-heartbeat]
* [Insightsölçümlerini][log-schema-insightsmetrics]
* [KubeEvents][log-schema-kubeevents]
* [KubeHealth][log-schema-kubehealth]
* [KubeMonAgentEvents][log-schema-kubemonagentevents]
* [Kubenodeınventory][log-schema-kubenodeinventory]
* [KubePodInventory][log-schema-kubepodinventory]
* [KubeServices][log-schema-kubeservices]
* [Erişen][log-schema-perf]

## <a name="log-roles"></a>Günlük rolleri

| Rol                     | Açıklama |
|--------------------------|-------------|
| *aksService*             | Denetim düzlemi işlemi için denetim günlüğünde görünen ad (hcpService 'ten) |
| *MasterClient*           | MasterClientCertificate için denetim günlüğünde görünen ad, az aks Get-Credentials öğesinden aldığınız sertifika |
| *nodeclient*             | Aracı düğümleri tarafından kullanılan ClientCertificate için görünen ad |

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, AKS kümenizdeki Kubernetes denetim düzlemi bileşenleri için günlüklerin nasıl etkinleştirileceğini ve gözden geçirildiğini öğrendiniz. Daha fazla izlemek ve sorun gidermek için [Kubelet günlüklerini görüntüleyebilir][kubelet-logs] ve [SSH düğüm erişimini etkinleştirebilirsiniz][aks-ssh].

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create

<!-- LINKS - internal -->
[cli-quickstart]: kubernetes-walkthrough.md
[portal-quickstart]: kubernetes-walkthrough-portal.md
[log-analytics-overview]: ../azure-monitor/logs/log-query-overview.md
[analyze-log-analytics]: ../azure-monitor/logs/log-analytics-tutorial.md
[kubelet-logs]: kubelet-logs.md
[aks-ssh]: ssh.md
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[log-schema-azureactivity]: /azure/azure-monitor/reference/tables/azureactivity
[log-schema-azurediagnostics]: /azure/azure-monitor/reference/tables/azurediagnostics
[log-schema-azuremetrics]: /azure/azure-monitor/reference/tables/azuremetrics
[log-schema-containerimageinventory]: /azure/azure-monitor/reference/tables/containerimageinventory
[log-schema-containerinventory]: /azure/azure-monitor/reference/tables/containerinventory
[log-schema-containerlog]: /azure/azure-monitor/reference/tables/containerlog
[log-schema-containernodeinventory]: /azure/azure-monitor/reference/tables/containernodeinventory
[log-schema-containerservicelog]: /azure/azure-monitor/reference/tables/containerservicelog
[log-schema-heartbeat]: /azure/azure-monitor/reference/tables/heartbeat
[log-schema-insightsmetrics]: /azure/azure-monitor/reference/tables/insightsmetrics
[log-schema-kubeevents]: /azure/azure-monitor/reference/tables/kubeevents
[log-schema-kubehealth]: /azure/azure-monitor/reference/tables/kubehealth
[log-schema-kubemonagentevents]: /azure/azure-monitor/reference/tables/kubemonagentevents
[log-schema-kubenodeinventory]: /azure/azure-monitor/reference/tables/kubenodeinventory
[log-schema-kubepodinventory]: /azure/azure-monitor/reference/tables/kubepodinventory
[log-schema-kubeservices]: /azure/azure-monitor/reference/tables/kubeservices
[log-schema-perf]: /azure/azure-monitor/reference/tables/perf