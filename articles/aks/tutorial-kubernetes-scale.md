---
title: Azure’da Kubernetes öğreticisi - Uygulamayı Ölçeklendirme
description: Bu Azure Kubernetes Service (AKS) öğreticisinde Kubernetes içindeki düğümleri ve podları ölçeklendirmenin yanı sıra yataya pod otomatik ölçeklendirme uygulamasını yapmayı öğreneceksiniz.
services: container-service
ms.topic: tutorial
ms.date: 01/12/2021
ms.custom: mvc
ms.openlocfilehash: a268d39ec514fc7b88b555221ece7dc044ca49ba
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767520"
---
# <a name="tutorial-scale-applications-in-azure-kubernetes-service-aks"></a>Öğretici: Azure Kubernetes Hizmeti’nde (AKS) uygulamaları ölçeklendirme

Öğreticileri izlediyseniz, AKS 'de çalışan bir Kubernetes kümeniz vardır ve örnek Azure oylama uygulamasını dağıttınız. Yedi öğreticinin beşinci parçası olan bu öğreticide, uygulamada pod’ları ölçeklendirirsiniz ve otomatik pod ölçeklendirmeyi denersiniz. Ayrıca, barındırılan iş yükleri için kümenin kapasitesini değiştirmek üzere Azure VM düğümlerinin sayısını ölçeklendirmeyi de öğrenirsiniz. Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:

> [!div class="checklist"]
> * Kubernetes düğümlerini ölçeklendirme
> * Uygulamanızı çalıştıran Kubernetes podlarını el ile ölçeklendirme
> * Uygulama ön ucunu çalıştıran otomatik ölçeklendirme podlarını yapılandırma

Sonraki öğreticilerde, Azure oy uygulaması yeni bir sürüme güncelleştirilir.

## <a name="before-you-begin"></a>Başlamadan önce

Önceki öğreticilerde, bir uygulama bir kapsayıcı görüntüsüne paketlendi. Bu görüntü, Azure Container Registry yüklendi ve bir AKS kümesi oluşturdunuz. Uygulama daha sonra AKS kümesine dağıtıldı. Bu adımları tamamlamadıysanız ve takip etmek istiyorsanız, [öğretici 1 – kapsayıcı görüntüleri oluşturma][aks-tutorial-prepare-app]ile başlayın.

Bu öğreticide, Azure CLı sürüm 2.0.53 veya üstünü çalıştırıyor olmanız gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure CLI yükleme][azure-cli-install].

## <a name="manually-scale-pods"></a>Pod’ları el ile ölçeklendirme

Önceki öğreticilerde Azure Vote ön ucu ve Redis örneği dağıtıldığında tek bir çoğaltma oluşturulmuştu. Kümenizdeki podların sayısını ve durumunu görmek için [kubectl get][kubectl-get] komutunu aşağıdaki şekilde kullanın:

```console
kubectl get pods
```

Aşağıdaki örnek çıktıda bir ön uç podu ve bir arka uç podu gösterilmektedir:

```output
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-2549686872-4d2r5   1/1       Running   0          31m
azure-vote-front-848767080-tf34m   1/1       Running   0          31m
```

*azure-vote-front* dağıtımındaki pod sayısını el ile değiştirmek için [kubectl scale][kubectl-scale] komutunu kullanın. Aşağıdaki örnekte ön uç podlarının sayısı *5*'e çıkarılmaktadır:

```console
kubectl scale --replicas=5 deployment/azure-vote-front
```

[Kubectl][kubectl-get] 'yi yeniden çalıştırarak, aks 'lerin ek düğüm 'leri başarıyla oluşturduğunu doğrulayın. Bir dakikadan sonra, kümelerinizi kümenizde bulabilirsiniz:

```console
kubectl get pods

                                    READY     STATUS    RESTARTS   AGE
azure-vote-back-2606967446-nmpcf    1/1       Running   0          15m
azure-vote-front-3309479140-2hfh0   1/1       Running   0          3m
azure-vote-front-3309479140-bzt05   1/1       Running   0          3m
azure-vote-front-3309479140-fvcvm   1/1       Running   0          3m
azure-vote-front-3309479140-hrbf2   1/1       Running   0          15m
azure-vote-front-3309479140-qphz8   1/1       Running   0          3m
```

## <a name="autoscale-pods"></a>Pod’ları otomatik ölçeklendirme

Kubernetes, bir dağıtımdaki pod’ların sayısını CPU kullanımı ve diğer seçili ölçümleri temel alarak ayarlamak için [yatay pod otomatik ölçeklendirmeyi][kubernetes-hpa] destekler. [Ölçüm Sunucusu][metrics-server], Kubernetes'e kaynak kullanımı sağlamak için kullanılır ve AKS kümesi 1.10 ve üzeri sürümlerde otomatik olarak dağıtılır. AKS kümenizin sürümünü görmek için aşağıdaki örnekte gösterildiği gibi [az aks show][az-aks-show] komutunu kullanın:

```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --query kubernetesVersion --output table
```

> [!NOTE]
> AKS kümeniz *1,10*'den küçükse, ölçüm sunucusu otomatik olarak yüklenmez. Ölçüm sunucusu yükleme bildirimleri `components.yaml` , ölçüm sunucusu sürümlerindeki bir varlık olarak kullanılabilir, bu da bunları bir URL aracılığıyla yükleyebileceğiniz anlamına gelir. Bu YAML tanımları hakkında daha fazla bilgi edinmek için README 'ın [dağıtım][metrics-server-github] bölümüne bakın.
> 
> Örnek yükleme:
> ```console
> kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/download/v0.3.6/components.yaml
> ```

Otomatik Scaler 'yi kullanmak için, yığınlarınızdaki tüm kapsayıcılar ve yığınlarınızın CPU istekleri ve sınırları tanımlı olmalıdır. `azure-vote-front`Dağıtımda, ön uç kapsayıcısı, 0,5 CPU sınırlaması ile 0,25 CPU talep ediyor. Bu kaynak istekleri ve limitleri aşağıdaki örnek kod parçacığında gösterildiği gibi tanımlanmıştır:

```yaml
resources:
  requests:
     cpu: 250m
  limits:
     cpu: 500m
```

Aşağıdaki örnek, *azure-vote-front* dağıtımındaki podların sayısını otomatik olarak ölçeklendirmek için [kubectl autoscale][kubectl-autoscale] komutunu kullanır. Tüm FID 'ler genelinde ortalama CPU kullanımı, istenen kullanımlarının %50 ' ünü aşarsa, otomatik Scaler, Pod 'leri en fazla *10* örneğe kadar artırır. Dağıtım için en az *3* örnek tanımlanmıştır:

```console
kubectl autoscale deployment azure-vote-front --cpu-percent=50 --min=3 --max=10
```

Alternatif olarak, otomatik Scaler davranışını ve kaynak sınırlarını tanımlamak için bir bildirim dosyası da oluşturabilirsiniz. Aşağıda adlı bir bildirim dosyası örneği verilmiştir `azure-vote-hpa.yaml` .

```yaml
apiVersion: autoscaling/v1
kind: HorizontalPodAutoscaler
metadata:
  name: azure-vote-back-hpa
spec:
  maxReplicas: 10 # define max replica count
  minReplicas: 3  # define min replica count
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: azure-vote-back
  targetCPUUtilizationPercentage: 50 # target CPU utilization

---

apiVersion: autoscaling/v1
kind: HorizontalPodAutoscaler
metadata:
  name: azure-vote-front-hpa
spec:
  maxReplicas: 10 # define max replica count
  minReplicas: 3  # define min replica count
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: azure-vote-front
  targetCPUUtilizationPercentage: 50 # target CPU utilization
```

`kubectl apply`Bildirim dosyasında tanımlanan otomatik Scaler 'ı uygulamak için kullanın `azure-vote-hpa.yaml` .

```console
kubectl apply -f azure-vote-hpa.yaml
```

Otomatik ölçeklendirme durumunu görmek için `kubectl get hpa` komutunu şu şekilde kullanın:

```
kubectl get hpa

NAME               REFERENCE                     TARGETS    MINPODS   MAXPODS   REPLICAS   AGE
azure-vote-front   Deployment/azure-vote-front   0% / 50%   3         10        3          2m
```

Birkaç dakika sonra Azure Vote uygulamasında en az yük ile, pod çoğaltmalarının sayısı otomatik olarak üçe iner. `kubectl get pods` komutunu tekrar kullanarak gereksiz podların kaldırıldığını görebilirsiniz.

## <a name="manually-scale-aks-nodes"></a>AKS düğümlerini el ile ölçeklendirme

Önceki öğreticideki komutları kullanarak Kubernetes kümenizi oluşturduysanız iki düğüm vardır. Kümenizde daha fazla veya daha az kapsayıcı iş yükü planlıyorsanız, düğüm sayısını el ile ayarlayabilirsiniz.

Aşağıdaki örnek, *myAKSCluster* adlı Kubernetes kümesinde düğümlerin sayısını üçe yükseltir. Komutun tamamlanması birkaç dakika sürer.

```azurecli
az aks scale --resource-group myResourceGroup --name myAKSCluster --node-count 3
```

Küme başarıyla ölçeklendirildiğinde, çıktı aşağıdaki örneğe benzer:

```output
"agentPoolProfiles": [
  {
    "count": 3,
    "dnsPrefix": null,
    "fqdn": null,
    "name": "myAKSCluster",
    "osDiskSizeGb": null,
    "osType": "Linux",
    "ports": null,
    "storageProfile": "ManagedDisks",
    "vmSize": "Standard_D2_v2",
    "vnetSubnetId": null
  }
```

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, Kubernetes kümenizde farklı ölçeklendirme özellikleri kullandınız. Şunları öğrendiniz:

> [!div class="checklist"]
> * Uygulamanızı çalıştıran Kubernetes podlarını el ile ölçeklendirme
> * Uygulama ön ucunu çalıştıran otomatik ölçeklendirme podlarını yapılandırma
> * Kubernetes düğümlerini el ile ölçeklendirin

Kubernetes’te uygulama güncelleştirmeyi öğrenmek için sonraki öğreticiye ilerleyin.

> [!div class="nextstepaction"]
> [Kubernetes'te uygulama güncelleştirme][aks-tutorial-update-app]

<!-- LINKS - external -->
[kubectl-autoscale]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#autoscale
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-scale]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#scale
[kubernetes-hpa]: https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/
[metrics-server-github]: https://github.com/kubernetes-sigs/metrics-server/blob/master/README.md#deployment
[metrics-server]: https://kubernetes.io/docs/tasks/debug-application-cluster/resource-metrics-pipeline/#metrics-server

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-update-app]: ./tutorial-kubernetes-app-update.md
[az-aks-scale]: /cli/azure/aks#az_aks_scale
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az_aks_show
