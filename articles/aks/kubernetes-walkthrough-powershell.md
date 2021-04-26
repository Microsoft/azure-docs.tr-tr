---
title: 'Hızlı başlangıç: PowerShell kullanarak AKS kümesi dağıtma'
description: Hızlı bir şekilde bir Kubernetes kümesi oluşturmayı, uygulamayı dağıtmayı ve PowerShell kullanarak Azure Kubernetes Service (AKS) içindeki performansı izlemeyi öğrenin.
services: container-service
ms.topic: quickstart
ms.date: 03/15/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2b61c791390200beac4a18422a4de58dd94fa711
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103492906"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-cluster-using-powershell"></a>Hızlı başlangıç: PowerShell kullanarak bir Azure Kubernetes hizmet kümesi dağıtma

Azure Kubernetes hizmeti (AKS), kümelerinizi hızlı bir şekilde dağıtmanıza ve yönetmenize olanak tanıyan bir yönetilen Kubernetes hizmetidir. Bu hızlı başlangıçta şunları yapmanız gerekir:
* PowerShell kullanarak bir AKS kümesi dağıtın. 
* Kümede bir Web ön ucu ve bir Redsıs örneği ile çok kapsayıcılı bir uygulama çalıştırın. 
* Uygulamanızı çalıştıran kümenin ve yığınların sistem durumunu izleyin.

Windows Server düğüm havuzu oluşturma hakkında daha fazla bilgi edinmek için bkz. [Windows Server kapsayıcılarını destekleyen AKS kümesi oluşturma][windows-container-powershell].

![Azure Kubernetes hizmetinde dağıtılan oylama uygulaması](./media/kubernetes-walkthrough-powershell/voting-app-deployed-in-azure-kubernetes-service.png)

Bu hızlı başlangıç, Kubernetes kavramlarının temel olarak bilindiğini varsayar. Daha fazla bilgi için bkz. [Azure Kubernetes hizmeti (AKS) Için Kubernetes temel kavramları][kubernetes-concepts].

## <a name="prerequisites"></a>Önkoşullar

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun.

PowerShell 'i yerel olarak çalıştırıyorsanız, az PowerShell modülünü yükledikten sonra [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) cmdlet 'Ini kullanarak Azure hesabınıza bağlanın. Az PowerShell modülünü yükleme hakkında daha fazla bilgi için bkz. [yükleme Azure PowerShell][install-azure-powershell].

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Birden çok Azure aboneliğiniz varsa, [set-AzContext](/powershell/module/az.accounts/set-azcontext) cmdlet 'ini kullanarak kaynakların faturalandırılması gereken uygun abonelik kimliğini seçin.

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

[Azure Kaynak grubu](../azure-resource-manager/management/overview.md) , Azure kaynaklarının dağıtıldığı ve yönetildiği mantıksal bir gruptur. Bir kaynak grubu oluşturduğunuzda bir konum belirtmeniz istenecektir. Bu konum: 
* Kaynak grubu meta verilerinizin depolama konumu.
* Kaynak oluşturma sırasında başka bir bölge belirtmezseniz kaynaklarınızın Azure 'da çalışacağı yer. 

Aşağıdaki örnek **eastus** bölgesinde **myresourcegroup** adlı bir kaynak grubu oluşturur.

[New-AzResourceGroup][new-azresourcegroup] cmdlet 'ini kullanarak bir kaynak grubu oluşturun.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location eastus
```

Başarıyla oluşturulan kaynak grubu için çıkış:

```plaintext
ResourceGroupName : myResourceGroup
Location          : eastus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup
```

## <a name="create-aks-cluster"></a>AKS kümesi oluşturma

1. Komut satırı yardımcı programını kullanarak bir SSH anahtar çifti oluşturun `ssh-keygen` . 
    * Daha fazla ayrıntı için bkz. [hızlı adımlar: Azure 'Da Linux VM 'ler IÇIN SSH genel-özel anahtar çifti oluşturma ve kullanma](../virtual-machines/linux/mac-create-ssh-keys.md).

1. [New-AzAks][new-azaks] cmdlet 'ini kullanarak bir aks kümesi oluşturun. Kapsayıcılar için Azure Izleyici varsayılan olarak etkindir.

    Aşağıdaki örnekte, bir düğüm ile **myAKSCluster** adlı bir küme oluşturulmuştur. 

    ```azurepowershell-interactive
    New-AzAksCluster -ResourceGroupName myResourceGroup -Name myAKSCluster -NodeCount 1
    ```

Birkaç dakika sonra komut tamamlanır ve küme hakkında bilgi döndürür.

> [!NOTE]
> AKS kümesi oluşturduğunuzda, AKS kaynaklarını depolamak için otomatik olarak ikinci bir kaynak grubu oluşturulur. Daha fazla bilgi için bkz. [neden AKS ile iki kaynak grubu oluşturulur?](./faq.md#why-are-two-resource-groups-created-with-aks)

## <a name="connect-to-the-cluster"></a>Kümeye bağlanma

Bir Kubernetes kümesini yönetmek için Kubernetes komut satırı istemcisini ( [kubectl][kubectl]) kullanın. `kubectl` Azure Cloud Shell kullanıyorsanız, zaten yüklüdür. 

1. `kubectl`Cmdlet 'ini kullanarak yerel olarak yükler `Install-AzAksKubectl` :

    ```azurepowershell
    Install-AzAksKubectl
    ```

2. `kubectl` [Import-AzAksCredential][import-azakscredential] cmdlet 'Ini kullanarak Kubernetes kümenize bağlanacak şekilde yapılandırın. Aşağıdaki cmdlet kimlik bilgilerini indirir ve Kubernetes CLı 'yi bunları kullanacak şekilde yapılandırır.

    ```azurepowershell-interactive
    Import-AzAksCredential -ResourceGroupName myResourceGroup -Name myAKSCluster
    ```

3. [Kubectl Get][kubectl-get] komutunu kullanarak kümenizin bağlantısını doğrulayın. Bu komut, küme düğümlerinin bir listesini döndürür.

    ```azurepowershell-interactive
    .\kubectl get nodes
    ```

    Çıktı, önceki adımlarda oluşturulan tek düğümü gösterir. Düğüm durumunun *Ready* olduğundan emin olun:

    ```plaintext
    NAME                       STATUS   ROLES   AGE     VERSION
    aks-nodepool1-31718369-0   Ready    agent   6m44s   v1.15.10
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

    ```azurepowershell-interactive
    .\kubectl apply -f azure-vote.yaml
    ```

    Çıktıda başarıyla oluşturulan dağıtımlar ve hizmetler gösterilmektedir:

    ```plaintext
    deployment.apps/azure-vote-back created
    service/azure-vote-back created
    deployment.apps/azure-vote-front created
    service/azure-vote-front created
    ```

## <a name="test-the-application"></a>Uygulamayı test edin

Uygulama çalıştığında, bir Kubernetes hizmeti, uygulamanın ön ucuna internet 'e koyar. Bu işlemin tamamlanması birkaç dakika sürebilir.

[Kubectl Get Service][kubectl-get] komutunu bağımsız değişkeniyle kullanarak ilerlemeyi izleyin `--watch` .

```azurepowershell-interactive
.\kubectl get service azure-vote-front --watch
```

Hizmetin **dış IP** çıkışı `azure-vote-front` Başlangıçta *Beklemede* olarak gösterilir.

```plaintext
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

**Dış IP** adresi *bekliyor* durumundan gerçek ortak IP adresine değiştiğinde, `CTRL-C` izleme işlemini durdurmak için kullanın `kubectl` . Aşağıdaki örnek çıktıda, hizmete atanmış geçerli bir genel IP adresi gösterilmektedir:

```plaintext
azure-vote-front   LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Azure oy uygulamasını çalışırken görmek için, hizmetinizin dış IP adresine bir Web tarayıcısı açın.

![Azure Kubernetes hizmetinde dağıtılan oylama uygulaması](./media/kubernetes-walkthrough-powershell/voting-app-deployed-in-azure-kubernetes-service.png)

Azure portal kapsayıcılar için Azure Izleyici tarafından yakalanan küme düğümlerini ve düğüm ölçümlerini görüntüleyin. 

## <a name="delete-the-cluster"></a>Küme silme

Azure ücretlerinden kaçınmak için gereksiz kaynaklarınızı temizleyin. Kaynak grubunu, kapsayıcı hizmetini ve ilgili tüm kaynakları kaldırmak için [Remove-AzResourceGroup][remove-azresourcegroup] cmdlet 'ini kullanın.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
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
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[azure-dev-spaces]: ../dev-spaces/index.yml
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[azure-vote-app]: https://github.com/Azure-Samples/azure-voting-app-redis.git

<!-- LINKS - internal -->
[windows-container-powershell]: windows-container-powershell.md
[kubernetes-concepts]: concepts-clusters-workloads.md
[install-azure-powershell]: /powershell/azure/install-az-ps
[new-azresourcegroup]: /powershell/module/az.resources/new-azresourcegroup
[new-azaks]: /powershell/module/az.aks/new-azaks
[import-azakscredential]: /powershell/module/az.aks/import-azakscredential
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[remove-azresourcegroup]: /powershell/module/az.resources/remove-azresourcegroup
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[kubernetes-dashboard]: kubernetes-dashboard.md
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
