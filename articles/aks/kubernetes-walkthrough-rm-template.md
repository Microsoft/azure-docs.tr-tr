---
title: Hızlı başlangıç-Azure Kubernetes hizmeti (AKS) kümesi oluşturma
description: Azure Resource Manager şablonu kullanarak bir Kubernetes kümesini hızlıca oluşturmayı ve Azure Kubernetes Service (AKS) içinde uygulama dağıtmayı öğrenin
services: container-service
ms.topic: quickstart
ms.date: 03/15/2021
ms.custom: mvc,subject-armqs, devx-track-azurecli
ms.openlocfilehash: 4bcaafdb1f465fb8568078dfb5bfaf988d0cd587
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764450"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-aks-cluster-using-an-arm-template"></a>Hızlı başlangıç: ARM şablonunu kullanarak bir Azure Kubernetes hizmeti (AKS) kümesi dağıtma

Azure Kubernetes hizmeti (AKS), kümelerinizi hızlı bir şekilde dağıtmanıza ve yönetmenize olanak tanıyan bir yönetilen Kubernetes hizmetidir. Bu hızlı başlangıçta şunları yapmanız gerekir:
* Bir Azure Resource Manager şablonu kullanarak AKS kümesi dağıtın. 
* Kümede bir Web ön ucu ve bir Redsıs örneği ile çok kapsayıcılı bir uygulama çalıştırın. 

![Azure Vote’a göz atma görüntüsü](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Bu hızlı başlangıç, Kubernetes kavramlarının temel olarak bilindiğini varsayar. Daha fazla bilgi için bkz. [Azure Kubernetes hizmeti (AKS) Için Kubernetes temel kavramları][kubernetes-concepts].

Ortamınız önkoşulları karşılıyorsa ve ARM şablonlarını kullanma hakkında bilginiz varsa, **Azure’a dağıtma** düğmesini seçin. Şablon Azure portalda açılır.

[![Azure’a dağıtma](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-aks%2Fazuredeploy.json)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Bu makale, Azure CLı 'nin sürüm 2.0.61 veya üstünü gerektirir. Azure Cloud Shell kullanılıyorsa, en son sürüm zaten yüklüdür.

- Kaynak Yöneticisi şablonu kullanarak AKS kümesi oluşturmak için bir SSH ortak anahtarı sağlarsınız. Bu kaynağa ihtiyacınız varsa, aşağıdaki bölüme bakın; Aksi takdirde, [şablonu gözden geçirme](#review-the-template) bölümüne atlayın.

### <a name="create-an-ssh-key-pair"></a>SSH anahtar çifti oluşturma

AKS düğümlerine erişmek için, komutunu kullanarak oluşturduğunuz bir SSH anahtar çifti (public ve Private) kullanarak bağlanırsınız `ssh-keygen` . Varsayılan olarak, bu dosyalar *~/PST SSH* dizininde oluşturulur. Komutun çalıştırılması, `ssh-keygen` belirtilen konumda zaten mevcut olan tüm SSH anahtar çiftinin üzerine yazar.

1. [https://shell.azure.com](https://shell.azure.com)Tarayıcınızda Cloud Shell açmak için bölümüne gidin.

1. `ssh-keygen` komutunu çalıştırın. Aşağıdaki örnek, RSA şifrelemesini ve 2048 bit uzunluğunu kullanarak bir SSH anahtar çifti oluşturur:

    ```console
    ssh-keygen -t rsa -b 2048
    ```

SSH anahtarları oluşturma hakkında daha fazla bilgi için bkz. [Azure 'da kimlik doğrulaması IÇIN SSH anahtarları oluşturma ve yönetme][ssh-keys].

## <a name="review-the-template"></a>Şablonu gözden geçirme

Bu hızlı başlangıçta kullanılan şablon [Azure Hızlı Başlangıç şablonlarından](https://azure.microsoft.com/resources/templates/101-aks/) alınmıştır.

:::code language="json" source="~/quickstart-templates/101-aks/azuredeploy.json":::

Daha fazla AKS örneği için bkz. [aks hızlı başlangıç şablonları][aks-quickstart-templates] sitesi.

## <a name="deploy-the-template"></a>Şablonu dağıtma

1. Azure 'da oturum açmak ve bir şablonu açmak için aşağıdaki düğmeyi seçin.

    [![Azure’a dağıtma](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-aks%2Fazuredeploy.json)

2. Aşağıdaki değerleri seçin veya girin.

    Bu hızlı başlangıçta, *Işletim sistemi disk boyutu GB*, *Aracı sayısı*, *Aracı VM boyutu*, *Işletim sistemi türü* ve *Kubernetes sürümü* için varsayılan değerleri bırakın. Aşağıdaki şablon parametreleri için kendi değerlerinizi sağlayın:

    * **Abonelik**: bir Azure aboneliği seçin.
    * **Kaynak grubu**: **Yeni oluştur**' u seçin. Kaynak grubu için *Myresourcegroup* gibi benzersiz bir ad girin ve ardından **Tamam**' ı seçin.
    * **Konum**: **Doğu ABD** gibi bir konum seçin.
    * **Küme adı**: aks kümesi Için *Myakscluster* gibi benzersiz bir ad girin.
    * **DNS ön eki**: kümeniz için *myakscluster* gibi benzersiz bir DNS öneki girin.
    * **Linux Yöneticisi Kullanıcı adı**: SSH kullanarak bağlanmak için *azureuser* gibi bir Kullanıcı adı girin.
    * **Ssh rsa ortak anahtarı**: SSH anahtar çiftin *genel* bölümünü kopyalayıp yapıştırın (varsayılan olarak, *~/. ssh/id_rsa. pub* içeriğini).

    ![Portalda Azure Kubernetes hizmet kümesi oluşturmak için şablon Kaynak Yöneticisi](./media/kubernetes-walkthrough-rm-template/create-aks-cluster-using-template-portal.png)

3. **Gözden geçir + Oluştur**’u seçin.

AKS kümesinin oluşturulması birkaç dakika sürer. Sonraki adıma geçmeden önce kümenin başarılı bir şekilde dağıtılmasını bekleyin.

## <a name="validate-the-deployment"></a>Dağıtımı doğrulama

### <a name="connect-to-the-cluster"></a>Kümeye bağlanma

Bir Kubernetes kümesini yönetmek için Kubernetes komut satırı istemcisini ( [kubectl][kubectl]) kullanın. `kubectl` Azure Cloud Shell kullanıyorsanız, zaten yüklüdür. 

1. `kubectl` [Az aks install-cli][az-aks-install-cli] komutunu kullanarak yerel olarak yükler:

    ```azurecli
    az aks install-cli
    ```

2. `kubectl` [Az aks Get-Credentials][az-aks-get-credentials] komutunu kullanarak Kubernetes kümenize bağlanacak şekilde yapılandırın. Bu komut, kimlik bilgilerini indirir ve Kubernetes CLı 'yi bunları kullanacak şekilde yapılandırır.

    ```azurecli-interactive
    az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
    ```

3. [Kubectl Get][kubectl-get] komutunu kullanarak kümenizin bağlantısını doğrulayın. Bu komut, küme düğümlerinin bir listesini döndürür.

    ```console
    kubectl get nodes
    ```

    Çıktı, önceki adımlarda oluşturulan düğümleri gösterir. Tüm düğümlerin durumunun *hazırlanmaya* çalıştığından emin olun:

    ```output
    NAME                       STATUS   ROLES   AGE     VERSION
    aks-agentpool-41324942-0   Ready    agent   6m44s   v1.12.6    
    aks-agentpool-41324942-1   Ready    agent   6m46s   v1.12.6
    aks-agentpool-41324942-2   Ready    agent   6m45s   v1.12.6
    ```

### <a name="run-the-application"></a>Uygulamayı çalıştırma

Bir [Kubernetes bildirim dosyası][kubernetes-deployment] , hangi kapsayıcı görüntülerinin çalıştırılacağı gibi kümenin istenen durumunu tanımlar. 

Bu hızlı başlangıçta, [Azure oy uygulamasını][azure-vote-app]çalıştırmak için gerekli tüm nesneleri oluşturmak için bir bildirim kullanacaksınız. Bu bildirim iki [Kubernetes dağıtımı][kubernetes-deployment]içerir:
* Örnek Azure oyunu Python uygulamaları.
* Redsıs örneği. 

İki [Kubernetes hizmeti][kubernetes-service] de oluşturulur:
* Redsıs örneği için bir iç hizmet.
* Azure oy uygulamasına internet 'ten erişmek için bir dış hizmet.

1. `azure-vote.yaml` adlı bir dosya oluşturun.
    * Azure Cloud Shell kullanırsanız, bu dosya kullanılarak `vi` veya `nano` bir sanal veya fiziksel sistemde çalışırken oluşturulabilir.
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

### <a name="test-the-application"></a>Uygulamayı test edin

Uygulama çalıştığında, bir Kubernetes hizmeti, uygulamanın ön ucuna internet 'e koyar. Bu işlemin tamamlanması birkaç dakika sürebilir.

[Kubectl Get Service][kubectl-get] komutunu bağımsız değişkeniyle kullanarak ilerlemeyi izleyin `--watch` .

```console
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

![Azure Vote’a göz atma görüntüsü](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

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
[azure-dev-spaces]: ../dev-spaces/index.yml
[aks-quickstart-templates]: https://azure.microsoft.com/resources/templates/?term=Azure+Kubernetes+Service

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
[azure-cli-install]: /cli/azure/install-azure-cli
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[azure-portal]: https://portal.azure.com
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[kubernetes-dashboard]: kubernetes-dashboard.md
[ssh-keys]: ../virtual-machines/linux/create-ssh-keys-detailed.md
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
