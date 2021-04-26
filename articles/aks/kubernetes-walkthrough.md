---
title: 'Hızlı başlangıç: Azure CLı kullanarak AKS kümesi dağıtma'
description: Hızlı bir şekilde bir Kubernetes kümesi oluşturmayı, uygulamayı dağıtmayı ve Azure CLı kullanarak Azure Kubernetes Service (AKS) içindeki performansı izlemeyi öğrenin.
services: container-service
ms.topic: quickstart
ms.date: 02/26/2021
ms.custom:
- H1Hack27Feb2017
- mvc
- devcenter
- seo-javascript-september2019
- seo-javascript-october2019
- seo-python-october2019
- devx-track-azurecli
- contperf-fy21q1
ms.openlocfilehash: 8adfd1a6e26a3381653ca9a794b124e201b9d481
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106106747"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-cluster-using-the-azure-cli"></a>Hızlı başlangıç: Azure CLı kullanarak bir Azure Kubernetes hizmet kümesi dağıtma

Azure Kubernetes hizmeti (AKS), kümelerinizi hızlı bir şekilde dağıtmanıza ve yönetmenize olanak tanıyan bir yönetilen Kubernetes hizmetidir. Bu hızlı başlangıçta şunları yapmanız gerekir:
* Azure CLı kullanarak bir AKS kümesi dağıtın. 
* Kümede bir Web ön ucu ve bir Redsıs örneği ile çok kapsayıcılı bir uygulama çalıştırın. 
* Uygulamanızı çalıştıran kümenin ve yığınların sistem durumunu izleyin.

  ![Azure Kubernetes hizmetinde dağıtılan oylama uygulaması](./media/container-service-kubernetes-walkthrough/voting-app-deployed-in-azure-kubernetes-service.png)

Bu hızlı başlangıç, Kubernetes kavramlarının temel olarak bilindiğini varsayar. Daha fazla bilgi için bkz. [Azure Kubernetes hizmeti (AKS) Için Kubernetes temel kavramları][kubernetes-concepts].

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Windows Server düğüm havuzu oluşturma hakkında daha fazla bilgi edinmek için bkz. [Windows Server kapsayıcılarını destekleyen AKS kümesi oluşturma][windows-container-cli].

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Bu makale, Azure CLı 'nin sürüm 2.0.64 veya üstünü gerektirir. Azure Cloud Shell kullanılıyorsa, en son sürüm zaten yüklüdür.

> [!NOTE]
> Komutları bu hızlı başlangıçta Azure Cloud Shell yerine yerel olarak çalıştırmayı planlıyorsanız, komutları yönetici olarak çalıştırın.

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

[Azure Kaynak grubu](../azure-resource-manager/management/overview.md) , Azure kaynaklarının dağıtıldığı ve yönetildiği mantıksal bir gruptur. Bir kaynak grubu oluşturduğunuzda bir konum belirtmeniz istenecektir. Bu konum: 
* Kaynak grubu meta verilerinizin depolama konumu.
* Kaynak oluşturma sırasında başka bir bölge belirtmezseniz kaynaklarınızın Azure 'da çalışacağı yer. 

Aşağıdaki örnek *eastus* konumunda *myResourceGroup* adlı bir kaynak grubu oluşturur.

[Az Group Create][az-group-create] komutunu kullanarak bir kaynak grubu oluşturun.


```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Başarıyla oluşturulan kaynak grubu için çıkış:

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup",
  "location": "eastus",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="enable-cluster-monitoring"></a>Küme izlemeyi etkinleştir

1. *Microsoft. OperationsManagement* ve *Microsoft. operationalınsights* 'ın aboneliğinizde kayıtlı olduğunu doğrulayın. Kayıt durumunu denetlemek için:

    ```azurecli
    az provider show -n Microsoft.OperationsManagement -o table
    az provider show -n Microsoft.OperationalInsights -o table
    ```
 
    Bunlar kaydedilmemişse *Microsoft. OperationsManagement* ve *Microsoft. operationalınsights* 'ı kullanarak kaydolun:
 
    ```azurecli
    az provider register --namespace Microsoft.OperationsManagement
    az provider register --namespace Microsoft.OperationalInsights
    ```

2. *--Enable-addons izleme* parametresini kullanarak [kapsayıcılar için Azure izleyicisini][azure-monitor-containers] etkinleştirin. 

## <a name="create-aks-cluster"></a>AKS kümesi oluşturma

[Az aks Create][az-aks-create] komutunu kullanarak bir aks kümesi oluşturun. Aşağıdaki örnek, bir düğüm ile *Myakscluster* adlı bir küme oluşturur: 

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster --node-count 1 --enable-addons monitoring --generate-ssh-keys
```

Birkaç dakika sonra komut tamamlanır ve küme hakkında JSON biçimli bilgileri döndürür.

> [!NOTE]
> AKS kümesi oluşturduğunuzda, AKS kaynaklarını depolamak için otomatik olarak ikinci bir kaynak grubu oluşturulur. Daha fazla bilgi için bkz. [neden AKS ile iki kaynak grubu oluşturulur?](./faq.md#why-are-two-resource-groups-created-with-aks)

## <a name="connect-to-the-cluster"></a>Kümeye bağlanma

Bir Kubernetes kümesini yönetmek için Kubernetes komut satırı istemcisini ( [kubectl][kubectl]) kullanın. `kubectl` Azure Cloud Shell kullanıyorsanız, zaten yüklüdür. 

1. `kubectl` [Az aks install-cli][az-aks-install-cli] komutunu kullanarak yerel olarak yükler:

    ```azurecli
    az aks install-cli
    ```

2. `kubectl` [Az aks Get-Credentials][az-aks-get-credentials] komutunu kullanarak Kubernetes kümenize bağlanacak şekilde yapılandırın. Aşağıdaki komut:  
      * Kimlik bilgilerini indirir ve Kubernetes CLı 'yi bunları kullanacak şekilde yapılandırır.
      * `~/.kube/config`, [Kubernetes yapılandırma dosyası][kubeconfig-file]için varsayılan konumu kullanır. *--File* kullanarak Kubernetes yapılandırma dosyanız için farklı bir konum belirtin.


    ```azurecli-interactive
    az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
    ```

3. [Kubectl Get][kubectl-get] komutunu kullanarak kümenizin bağlantısını doğrulayın. Bu komut, küme düğümlerinin bir listesini döndürür.

    ```azurecli-interactive
    kubectl get nodes
    ```

    Çıktı, önceki adımlarda oluşturulan tek düğümü gösterir. Düğüm durumunun *Ready* olduğundan emin olun:

    ```output
    NAME                       STATUS   ROLES   AGE     VERSION
    aks-nodepool1-31718369-0   Ready    agent   6m44s   v1.12.8
    ```

## <a name="run-the-application"></a>Uygulamayı çalıştırma

Bir [Kubernetes bildirim dosyası][kubernetes-deployment] , hangi kapsayıcı görüntülerinin çalıştırılacağı gibi kümenin istenen durumunu tanımlar. 

Bu hızlı başlangıçta, [Azure oy uygulamasını][azure-vote-app]çalıştırmak için gerekli tüm nesneleri oluşturmak için bir bildirim kullanacaksınız. Bu bildirim iki [Kubernetes dağıtımı][kubernetes-deployment]içerir:
* Örnek Azure oyunu Python uygulamaları.
* Redsıs örneği. 

İki [Kubernetes hizmeti][kubernetes-service] de oluşturulur:
* Redsıs örneği için bir iç hizmet.
* Azure oy uygulamasına internet 'ten erişmek için bir dış hizmet.

1. `azure-vote.yaml` adlı bir dosya oluşturun.
    * Azure Cloud Shell kullanırsanız, bu dosya, `code` `vi` veya `nano` bir sanal veya fiziksel sistemde çalışırken, veya kullanılarak oluşturulabilir
1. Aşağıdaki YAML tanımına kopyalayın:

    ```yaml
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: azure-vote-back
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: azure-vote-back
      template:
        metadata:
          labels:
            app: azure-vote-back
        spec:
          nodeSelector:
            "beta.kubernetes.io/os": linux
          containers:
          - name: azure-vote-back
            image: mcr.microsoft.com/oss/bitnami/redis:6.0.8
            env:
            - name: ALLOW_EMPTY_PASSWORD
              value: "yes"
            resources:
              requests:
                cpu: 100m
                memory: 128Mi
              limits:
                cpu: 250m
                memory: 256Mi
            ports:
            - containerPort: 6379
              name: redis
    ---
    apiVersion: v1
    kind: Service
    metadata:
      name: azure-vote-back
    spec:
      ports:
      - port: 6379
      selector:
        app: azure-vote-back
    ---
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: azure-vote-front
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: azure-vote-front
      template:
        metadata:
          labels:
            app: azure-vote-front
        spec:
          nodeSelector:
            "beta.kubernetes.io/os": linux
          containers:
          - name: azure-vote-front
            image: mcr.microsoft.com/azuredocs/azure-vote-front:v1
            resources:
              requests:
                cpu: 100m
                memory: 128Mi
              limits:
                cpu: 250m
                memory: 256Mi
            ports:
            - containerPort: 80
            env:
            - name: REDIS
              value: "azure-vote-back"
    ---
    apiVersion: v1
    kind: Service
    metadata:
      name: azure-vote-front
    spec:
      type: LoadBalancer
      ports:
      - port: 80
      selector:
        app: azure-vote-front
    ```

1. [Kubectl Apply][kubectl-apply] komutunu kullanarak uygulamayı dağıtın ve YAML bildiriminizde adı belirtin:

    ```console
    kubectl apply -f azure-vote.yaml
    ```

    Çıktıda başarıyla oluşturulan dağıtımlar ve hizmetler gösterilmektedir:

    ```output
    deployment "azure-vote-back" created
    service "azure-vote-back" created
    deployment "azure-vote-front" created
    service "azure-vote-front" created
    ```

## <a name="test-the-application"></a>Uygulamayı test edin

Uygulama çalıştığında, bir Kubernetes hizmeti, uygulamanın ön ucuna internet 'e koyar. Bu işlemin tamamlanması birkaç dakika sürebilir.

[Kubectl Get Service][kubectl-get] komutunu bağımsız değişkeniyle kullanarak ilerlemeyi izleyin `--watch` .

```azurecli-interactive
kubectl get service azure-vote-front --watch
```

Hizmetin **dış IP** çıkışı `azure-vote-front` Başlangıçta *Beklemede* olarak gösterilir.

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

**Dış IP** adresi *bekliyor* durumundan gerçek ortak IP adresine değiştiğinde, `CTRL-C` izleme işlemini durdurmak için kullanın `kubectl` . Aşağıdaki örnek çıktıda, hizmete atanmış geçerli bir genel IP adresi gösterilmektedir:

```output
azure-vote-front   LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Azure oy uygulamasını çalışırken görmek için, hizmetinizin dış IP adresine bir Web tarayıcısı açın.

![Azure Kubernetes hizmetinde dağıtılan oylama uygulaması](./media/container-service-kubernetes-walkthrough/voting-app-deployed-in-azure-kubernetes-service.png)

Azure portal [kapsayıcılar Için Azure izleyici][azure-monitor-containers] tarafından yakalanan küme düğümlerini ve düğüm ölçümlerini görüntüleyin. 

## <a name="delete-the-cluster"></a>Küme silme

Azure ücretlerinden kaçınmak için gereksiz kaynaklarınızı temizleyin. [az group delete][az-group-delete] komutunu kullanarak kaynak grubunu, kapsayıcı hizmetini ve ilgili tüm kaynakları kaldırabilirsiniz.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> Kümeyi sildiğinizde, AKS kümesi tarafından kullanılan Azure Active Directory hizmet sorumlusu kaldırılmaz. Hizmet sorumlusunu kaldırma adımları için bkz. [AKS hizmet sorumlusuyla ilgili önemli noktalar ve silme][sp-delete].
> 
> Yönetilen bir kimlik kullandıysanız, kimlik platform tarafından yönetilir ve kaldırma gerektirmez.

## <a name="get-the-code"></a>Kodu alma

Önceden var olan kapsayıcı görüntüleri, bu hızlı başlangıçta bir Kubernetes dağıtımı oluşturmak için kullanıldı. İlgili uygulama kodu, Dockerfile ve Kubernetes bildirim dosyası [GitHub ' da kullanılabilir.][azure-vote-app]

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta bir Kubernetes kümesi dağıttınız ve daha sonra çok kapsayıcılı bir uygulama dağıttınız. AKS kümeniz için [Kubernetes web panosuna erişin][kubernetes-dashboard] .

AKS hakkında daha fazla bilgi ve dağıtım örneği için tam kod açıklaması için Kubernetes küme öğreticisine geçin.

> [!div class="nextstepaction"]
> [AKS öğreticisi][aks-tutorial]

<!-- LINKS - external -->
[azure-vote-app]: https://github.com/Azure-Samples/azure-voting-app-redis.git
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubeconfig-file]: https://kubernetes.io/docs/concepts/configuration/organize-cluster-access-kubeconfig/

<!-- LINKS - internal -->
[kubernetes-concepts]: concepts-clusters-workloads.md
[aks-monitor]: ../azure-monitor/containers/container-insights-onboard.md
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-browse]: /cli/azure/aks#az_aks_browse
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-aks-install-cli]: /cli/azure/aks#az_aks_install_cli
[az-group-create]: /cli/azure/group#az_group_create
[az-group-delete]: /cli/azure/group#az_group_delete
[azure-cli-install]: /cli/azure/install_azure_cli
[azure-monitor-containers]: ../azure-monitor/containers/container-insights-overview.md
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[azure-portal]: https://portal.azure.com
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[kubernetes-dashboard]: kubernetes-dashboard.md
[windows-container-cli]: windows-container-cli.md
