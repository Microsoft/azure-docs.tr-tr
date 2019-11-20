---
title: Hızlı başlangıç-Azure Kubernetes hizmeti (AKS) kümesi oluşturma
description: Azure Resource Manager şablonu kullanarak bir Kubernetes kümesini hızlıca oluşturmayı ve Azure Kubernetes Service (AKS) içinde uygulama dağıtmayı öğrenin
services: container-service
author: mlearned
ms.service: container-service
ms.topic: quickstart
ms.date: 04/19/2019
ms.author: mlearned
ms.custom: mvc
ms.openlocfilehash: 307074618cae75ba57be219b4f975e2aec279682
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72255505"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-aks-cluster-using-an-azure-resource-manager-template"></a>Hızlı başlangıç: Azure Resource Manager şablonu kullanarak Azure Kubernetes hizmeti (AKS) kümesi dağıtma

Azure Kubernetes hizmeti (AKS), kümelerinizi hızlı bir şekilde dağıtmanıza ve yönetmenize olanak tanıyan bir yönetilen Kubernetes hizmetidir. Bu hızlı başlangıçta, bir Azure Resource Manager şablonu kullanarak bir AKS kümesi dağıtırsınız. Bir Web ön ucu ve bir Reda örneği içeren çok kapsayıcılı bir uygulama kümede çalıştırılır.

![Azure oylamaya göz atma görüntüsü](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

Bu hızlı başlangıçta, Kubernetes kavramlarının temel bir şekilde anlaşıldığı varsayılır. Daha fazla bilgi için bkz. [Azure Kubernetes hizmeti (AKS) Için Kubernetes temel kavramları][kubernetes-concepts].

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLı 'yi yerel olarak yükleyip kullanmayı tercih ederseniz bu hızlı başlangıç, Azure CLı sürüm 2.0.61 veya üstünü çalıştırıyor olmalıdır. Sürümü bulmak için `az --version` ' yı çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse bkz. [Azure CLI 'Yı yüklemek][azure-cli-install].

## <a name="prerequisites"></a>Prerequisites

Kaynak Yöneticisi şablonu kullanarak AKS kümesi oluşturmak için bir SSH ortak anahtarı ve Azure Active Directory hizmet sorumlusu sağlarsınız. Bu kaynaklardan herhangi birine ihtiyacınız varsa, aşağıdaki bölüme bakın; Aksi takdirde [AKS kümesi oluşturma](#create-an-aks-cluster) bölümüne atlayın.

### <a name="create-an-ssh-key-pair"></a>SSH anahtar çifti oluşturma

AKS düğümlerine erişmek için bir SSH anahtar çifti kullanarak bağlanırsınız. SSH ortak ve özel anahtar dosyaları oluşturmak için `ssh-keygen` komutunu kullanın. Varsayılan olarak, bu dosyalar *~/PST SSH* dizininde oluşturulur. Verilen konumda aynı ada sahip bir SSH anahtar çifti varsa, bu dosyaların üzerine yazılır.

Aşağıdaki komut RSA şifrelemesini ve 2048 bit uzunluğunu kullanarak bir SSH anahtar çifti oluşturur:

```azurecli-interactive
ssh-keygen -t rsa -b 2048
```

SSH anahtarları oluşturma hakkında daha fazla bilgi için bkz. [Azure 'da kimlik doğrulaması IÇIN SSH anahtarları oluşturma ve yönetme][ssh-keys].

### <a name="create-a-service-principal"></a>Hizmet sorumlusu oluşturma

Bir AKS kümesinin diğer Azure kaynaklarıyla etkileşime geçmesini sağlamak için bir Azure Active Directory hizmet sorumlusu kullanılır. [Az ad SP Create-for-RBAC][az-ad-sp-create-for-rbac] komutunu kullanarak bir hizmet sorumlusu oluşturun. @No__t-0 parametresi, tüm ek izinlerin atanmasını sınırlar. Varsayılan olarak, bu hizmet sorumlusu bir yıl için geçerlidir.

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

Çıktı aşağıdaki örneğe benzer:

```json
{
  "appId": "8b1ede42-d407-46c2-a1bc-6b213b04295f",
  "displayName": "azure-cli-2019-04-19-21-42-11",
  "name": "http://azure-cli-2019-04-19-21-42-11",
  "password": "27e5ac58-81b0-46c1-bd87-85b4ef622682",
  "tenant": "73f978cf-87f2-41bf-92ab-2e7ce012db57"
}
```

*AppID* ve *parolayı*bir yere göz önünde oluşturun. Bu değerler aşağıdaki adımlarda kullanılır.

## <a name="create-an-aks-cluster"></a>AKS kümesi oluşturma

Bu hızlı başlangıçta kullanılan şablon, [bir Azure Kubernetes hizmet kümesi dağıtmaktır](https://azure.microsoft.com/resources/templates/101-aks/). Daha fazla AKS örneği için bkz. [aks hızlı başlangıç şablonları][aks-quickstart-templates] sitesi.

1. Azure 'da oturum açmak ve bir şablonu açmak için aşağıdaki görüntüyü seçin.

    [![Azure 'a dağıtın](./media/kubernetes-walkthrough-rm-template/deploy-to-azure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-aks%2Fazuredeploy.json)

2. Aşağıdaki değerleri seçin veya girin.  

    Bu hızlı başlangıçta, *Işletim sistemi disk boyutu GB*, *Aracı sayısı*, *Aracı VM boyutu*, *Işletim sistemi türü*ve *Kubernetes sürümü*için varsayılan değerleri bırakın. Aşağıdaki şablon parametreleri için kendi değerlerinizi sağlayın:

    * **Abonelik**: bir Azure aboneliği seçin.
    * **Kaynak grubu**: **Yeni oluştur**' u seçin. Kaynak grubu için *Myresourcegroup*gibi benzersiz bir ad girin ve ardından **Tamam**' ı seçin.
    * **Konum**: **Doğu ABD**gibi bir konum seçin.
    * **Küme adı**: aks kümesi Için *Myakscluster*gibi benzersiz bir ad girin.
    * **DNS ön eki**: kümeniz için *myakscluster*gibi benzersiz bir DNS öneki girin.
    * **Linux Yöneticisi Kullanıcı adı**: SSH kullanarak bağlanmak için *azureuser*gibi bir Kullanıcı adı girin.
    * **Ssh rsa ortak anahtarı**: SSH anahtar çiftin *ortak* bölümünü kopyalayıp yapıştırın (varsayılan olarak, *~/. ssh/id_rsa.exe*' nin içeriğini).
    * **Hizmet sorumlusu Istemci kimliği**: `az ad sp create-for-rbac` komutundan hizmet sorumlusunun *AppID* 'sini kopyalayıp yapıştırın.
    * **Hizmet sorumlusu Istemci parolası**: hizmet sorumlunuzu @no__t -2 komutundan kopyalayıp yapıştırın.
    * **Yukarıdaki hüküm ve koşullar durumunu kabul ediyorum**: kabul etmek için bu kutuyu işaretleyin.

    ![Portalda Azure Kubernetes hizmet kümesi oluşturmak için şablon Kaynak Yöneticisi](./media/kubernetes-walkthrough-rm-template/create-aks-cluster-using-template-portal.png)

3. **Satın al**' ı seçin.

AKS kümesini oluşturmak birkaç dakika sürer. Sonraki adıma geçmeden önce kümenin başarılı bir şekilde dağıtılmasını bekleyin.

## <a name="connect-to-the-cluster"></a>Kümeye Bağlan

Kubernetes kümesini yönetmek için Kubernetes komut satırı istemcisi olan [kubectl][kubectl]'yi kullanırsınız. Azure Cloud Shell kullanırsanız, `kubectl` zaten yüklüdür. @No__t-0 ' ı yerel olarak yüklemek için [az aks install-cli][az-aks-install-cli] komutunu kullanın:

```azurecli
az aks install-cli
```

@No__t, Kubernetes kümenize bağlanacak şekilde yapılandırmak için [az aks Get-Credentials][az-aks-get-credentials] komutunu kullanın. Bu komut, kimlik bilgilerini indirir ve Kubernetes CLı 'yi bunları kullanacak şekilde yapılandırır.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Kümenizin bağlantısını doğrulamak için [kubectl Get][kubectl-get] komutunu kullanarak küme düğümlerinin bir listesini döndürün.

```azurecli-interactive
kubectl get nodes
```

Aşağıdaki örnek çıktı, önceki adımlarda oluşturulan düğümleri gösterir. Tüm düğümlerin durumunun *hazırlanmaya*çalıştığından emin olun:

```
NAME                       STATUS   ROLES   AGE     VERSION
aks-agentpool-41324942-0   Ready    agent   6m44s   v1.12.6
aks-agentpool-41324942-1   Ready    agent   6m46s   v1.12.6
aks-agentpool-41324942-2   Ready    agent   6m45s   v1.12.6
```

## <a name="run-the-application"></a>Uygulamayı çalıştırma

Bir Kubernetes bildirim dosyası, küme için, hangi kapsayıcı görüntülerinin çalıştırılacağı gibi istenen durumu tanımlar. Bu hızlı başlangıçta, Azure oy uygulamasını çalıştırmak için gerekli tüm nesneleri oluşturmak için bir bildirim kullanılır. Bu bildirimde iki [Kubernetes dağıtımı][kubernetes-deployment] vardır-bir örnek Azure oy Python uygulamaları ve diğeri de redin örneği için. İki [Kubernetes hizmeti][kubernetes-service] de oluşturulur; redsıs örneği için bir iç hizmet ve Azure oy uygulamasına internet 'ten erişmek için bir dış hizmet.

> [!TIP]
> Bu hızlı başlangıçta, uygulama bildirimlerinizi el ile oluşturarak AKS kümesine dağıtırsınız. Daha çok gerçek dünyada senaryolar için [Azure dev Spaces][azure-dev-spaces] kullanarak doğrudan aks kümesinde kodunuzun hatalarını hızla yineleyebilirsiniz ve hatalarını ayıklayabilirsiniz. İşletim sistemi platformları ve geliştirme ortamlarında geliştirme alanlarını kullanabilir ve takımınızda başkalarıyla birlikte çalışabilirsiniz.

@No__t-0 adlı bir dosya oluşturun ve aşağıdaki YAML tanımına kopyalayın. Azure Cloud Shell kullanırsanız, bu dosya `vi` veya `nano` kullanılarak bir sanal veya fiziksel sistemde çalışırken oluşturulabilir:

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
        image: redis
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
        image: microsoft/azure-vote-front:v1
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

[Kubectl Apply][kubectl-apply] komutunu kullanarak uygulamayı dağıtın ve YAML bildiriminizde adı belirtin:

```azurecli-interactive
kubectl apply -f azure-vote.yaml
```

Aşağıdaki örnek çıktıda başarıyla oluşturulan dağıtımlar ve hizmetler gösterilmektedir:

```
deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-the-application"></a>Uygulamayı test etme

Uygulama çalıştığında, bir Kubernetes hizmeti, uygulamanın ön ucuna internet 'e koyar. Bu işlemin tamamlanması birkaç dakika sürebilir.

İlerlemeyi izlemek için, [kubectl Get Service][kubectl-get] komutunu `--watch` bağımsız değişkeniyle birlikte kullanın.

```azurecli-interactive
kubectl get service azure-vote-front --watch
```

Başlangıçta *Azure-oyönme* hizmeti IÇIN *dış IP* , *Beklemede*olarak gösterilir.

```
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

*Dış IP* adresi *bekliyor* durumundan gerçek ortak ıp adresini değiştirdiğinde, `kubectl` izleme işlemini durdurmak için `CTRL-C` ' yi kullanın. Aşağıdaki örnek çıktıda, hizmete atanmış geçerli bir genel IP adresi gösterilmektedir:

```
azure-vote-front   LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Azure oy uygulamasını çalışırken görmek için, hizmetinizin dış IP adresine bir Web tarayıcısı açın.

![Azure oylamaya göz atma görüntüsü](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

## <a name="delete-cluster"></a>Kümeyi Sil

Küme artık gerekli değilse, [az Group Delete][az-group-delete] komutunu kullanarak kaynak grubunu, kapsayıcı hizmetini ve tüm ilgili kaynakları kaldırın.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> Kümeyi sildiğinizde, AKS kümesi tarafından kullanılan Azure Active Directory hizmet sorumlusu kaldırılmaz. Hizmet sorumlusunu kaldırma adımları için bkz. [aks hizmet sorumlusu konuları ve silme][sp-delete].

## <a name="get-the-code"></a>Kodu alın

Bu hızlı başlangıçta, bir Kubernetes dağıtımı oluşturmak için önceden oluşturulmuş kapsayıcı görüntüleri kullanıldı. İlgili uygulama kodu, Dockerfile ve Kubernetes bildirim dosyası GitHub ' da kullanılabilir.

[https://github.com/Azure-Samples/azure-voting-app-redis][azure-vote-app]

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta bir Kubernetes kümesi dağıttınız ve bu kümeye çok kapsayıcılı bir uygulama dağıttınız. Oluşturduğunuz küme için [Kubernetes web panosuna erişin][kubernetes-dashboard] .

AKS hakkında daha fazla bilgi edinmek ve dağıtım örneği için bir bütün kod üzerinde gezinmek için Kubernetes küme öğreticisine geçin.

> [!div class="nextstepaction"]
> [AKS öğreticisi][aks-tutorial]

<!-- LINKS - external -->
[azure-vote-app]: https://github.com/Azure-Samples/azure-voting-app-redis.git
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[azure-dev-spaces]: https://docs.microsoft.com/azure/dev-spaces/
[aks-quickstart-templates]: https://azure.microsoft.com/resources/templates/?term=Azure+Kubernetes+Service

<!-- LINKS - internal -->
[kubernetes-concepts]: concepts-clusters-workloads.md
[aks-monitor]: https://aka.ms/coingfonboarding
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-browse]: /cli/azure/aks?view=azure-cli-latest#az-aks-browse
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[azure-cli-install]: /cli/azure/install-azure-cli
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[azure-portal]: https://portal.azure.com
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[kubernetes-dashboard]: kubernetes-dashboard.md
[ssh-keys]: ../virtual-machines/linux/create-ssh-keys-detailed.md
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
