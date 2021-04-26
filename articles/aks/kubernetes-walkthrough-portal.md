---
title: 'Hızlı başlangıç: Azure portal kullanarak AKS kümesi dağıtma'
titleSuffix: Azure Kubernetes Service
description: Hızlı bir şekilde bir Kubernetes kümesi oluşturmayı, bir uygulamayı dağıtmayı ve Azure portal kullanarak Azure Kubernetes hizmeti 'nde (AKS) performansı izlemenizi öğrenin.
services: container-service
ms.topic: quickstart
ms.date: 03/15/2021
ms.custom: mvc, seo-javascript-october2019, contperf-fy21q3
ms.openlocfilehash: 28ba2ffd2007aeb45081cf66b05395a2b8456bf7
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779714"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-aks-cluster-using-the-azure-portal"></a>Hızlı başlangıç: Azure portal kullanarak bir Azure Kubernetes hizmeti (AKS) kümesi dağıtma

Azure Kubernetes hizmeti (AKS), kümelerinizi hızlı bir şekilde dağıtmanıza ve yönetmenize olanak tanıyan bir yönetilen Kubernetes hizmetidir. Bu hızlı başlangıçta şunları yapmanız gerekir:
* Azure portal kullanarak bir AKS kümesi dağıtın. 
* Kümede bir Web ön ucu ve bir Redsıs örneği ile çok kapsayıcılı bir uygulama çalıştırın. 
* Uygulamanızı çalıştıran kümenin ve yığınların sistem durumunu izleyin.

![Azure Vote örnek uygulamasına göz atma görüntüsü](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

Bu hızlı başlangıç, Kubernetes kavramlarının temel olarak bilindiğini varsayar. Daha fazla bilgi için bkz. [Azure Kubernetes hizmeti (AKS) Için Kubernetes temel kavramları][kubernetes-concepts].

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

[https://portal.azure.com](https://portal.azure.com) adresinden Azure portalında oturum açın.

## <a name="create-an-aks-cluster"></a>AKS kümesi oluşturma

1. Azure portalı menüsünde veya **Giriş** sayfasında **Kaynak oluştur**’u seçin.

2. **Kapsayıcılar** > **Kubernetes Hizmeti** seçeneklerini belirleyin.

3. **Temel Bilgiler** sayfasında aşağıdaki seçenekleri yapılandırın:
    - **Proje ayrıntıları**: 
        * Bir Azure **Aboneliği** seçin.
        * *Myresourcegroup* gibi bir Azure **kaynak grubu** seçin veya oluşturun.
    - **Küme ayrıntıları**: 
        * **Kubernetes kümesi adı** alanına *myAKSCluster* gibi bir ad girin. 
        * AKS kümesi için bir **bölge** ve **Kubernetes sürümü** seçin.
    - **Birincil düğüm havuzu**: 
        * AKS düğümleri için bir VM **Düğüm boyutu** seçin. AKS kümesi dağıtıldıktan sonra, sanal makine boyutu *değiştirilemez*.
        * Kümeye dağıtılacak düğüm sayısını seçin. Bu hızlı başlangıç **Düğüm sayısı** değerini *1* olarak belirleyin. Küme dağıtıldıktan sonra düğüm sayısı *ayarlanabilir*.
    
    ![AKS kümesi oluşturma - temel bilgileri sağlama](media/kubernetes-walkthrough-portal/create-cluster-basics.png)

4. **İleri ' yi seçin: tamamlandığında düğüm havuzları** .

5. Varsayılan **düğüm havuzları** seçeneklerini saklayın. Ekranın alt kısmındaki **İleri: kimlik doğrulama**' ya tıklayın.
    > [!CAUTION]
    > Yeni oluşturulan Azure AD hizmet sorumluları, yaymak ve kullanılabilir hale gelmesi birkaç dakika sürebilir ve bu, Azure portal "hizmet sorumlusu bulunamadı" hatalarına ve doğrulama hatalarına neden olabilir. Bu çarpmaya ulaşırsanız, lütfen azaltma için [sorun giderme makalemizi](troubleshooting.md#received-an-error-saying-my-service-principal-wasnt-found-or-is-invalid-when-i-try-to-create-a-new-cluster) ziyaret edin.

6. **Kimlik Doğrulaması** sayfasında aşağıdaki seçenekleri yapılandırın:
    - Yeni küme kimliğini şunlardan biriyle oluşturun:
        * **Kimlik doğrulama** alanından, **sistem tarafından açılan yönetilen kimlikle** veya
        * Hizmet sorumlusu kullanmak için **hizmet sorumlusu** seçme. 
            * Varsayılan hizmet sorumlusu oluşturmak için *(yeni) varsayılan hizmet sorumlusu* ' nı seçin veya
            * Mevcut bir tane kullanmak için *hizmet sorumlusu Yapılandır* ' ı seçin. Mevcut sorumlu SPN istemci KIMLIĞINI ve gizli anahtarını sağlamanız gerekir.
    - AKS kümenizde dağıtılan Kubernetes kaynaklarına erişim üzerinde daha ayrıntılı denetim sağlamak için Kubernetes rol tabanlı erişim denetimi (Kubernetes RBAC) seçeneğini etkinleştirin.

    Varsayılan olarak, *temel* ağ kullanılır ve kapsayıcılar Için Azure izleyici etkindir. 

7. Doğrulama tamamlandığında **İncele ve oluştur**’a ve sonra **Oluştur**’a tıklayın. 


8. AKS kümesinin oluşturulması birkaç dakika sürer. Dağıtımınız tamamlandığında, kaynağına aşağıdakilerden birini yapın:
    * **Kaynağa Git ' e** tıklayın veya
    * AKS kümesi kaynak grubuna göz atma ve AKS kaynağını seçme. 
        * Örnek başına küme panosu: *Myresourcegroup* için göz atma ve *Myakscluster* kaynağı seçme.

        ![Azure portalda örnek AKS panosu](media/kubernetes-walkthrough-portal/aks-portal-dashboard.png)

## <a name="connect-to-the-cluster"></a>Kümeye bağlanma

Bir Kubernetes kümesini yönetmek için Kubernetes komut satırı istemcisini ( [kubectl][kubectl]) kullanın. `kubectl` Azure Cloud Shell kullanıyorsanız, zaten yüklüdür. 

1. `>_`Azure Portal en üstündeki düğmeyi kullanarak Cloud Shell açın.

    ![Azure Cloud Shell'i portalda açma](media/kubernetes-walkthrough-portal/aks-cloud-shell.png)

    > [!NOTE]
    > Yerel bir kabuk yüklemesinde bu işlemleri gerçekleştirmek için:
    > 1. Azure CLı 'nın yüklü olduğunu doğrulayın.
    > 2. Komutunu kullanarak Azure 'a bağlanın `az login` .

2. `kubectl` [Az aks Get-Credentials][az-aks-get-credentials] komutunu kullanarak Kubernetes kümenize bağlanacak şekilde yapılandırın. Aşağıdaki komut, kimlik bilgilerini indirir ve Kubernetes CLı 'yi bunları kullanacak şekilde yapılandırır.

    ```azurecli
    az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
    ```

3. `kubectl get`Küme düğümlerinin bir listesini döndürmek için kullanarak kümenizin bağlantısını doğrulayın.

    ```console
    kubectl get nodes
    ```

    Çıktı, önceki adımlarda oluşturulan tek düğümü gösterir. Düğüm durumunun *Ready* olduğundan emin olun:

    ```output
    NAME                       STATUS    ROLES     AGE       VERSION
    aks-agentpool-14693408-0   Ready     agent     15m       v1.11.5
    ```

## <a name="run-the-application"></a>Uygulamayı çalıştırma

Bir Kubernetes bildirim dosyası, hangi kapsayıcı görüntülerinin çalıştırılacağı gibi kümenin istenen durumunu tanımlar. 

Bu hızlı başlangıçta, Azure oy uygulamasını çalıştırmak için gerekli tüm nesneleri oluşturmak için bir bildirim kullanacaksınız. Bu bildirim iki Kubernetes dağıtımı içerir:
* Örnek Azure oyunu Python uygulamaları.
* Redsıs örneği. 

İki Kubernetes hizmeti de oluşturulur:
* Redsıs örneği için bir iç hizmet.
* Azure oy uygulamasına internet 'ten erişmek için bir dış hizmet.

1. Cloud Shell, adlı bir dosya oluşturmak için bir düzenleyici kullanın `azure-vote.yaml` , örneğin:
    * `code azure-vote.yaml`
    * `nano azure-vote.yaml`veya 
    * `vi azure-vote.yaml`. 

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

1. Komutunu kullanarak uygulamayı dağıtın `kubectl apply` ve YAML bildirimin adını belirtin:

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

İlerlemeyi izlemek için `kubectl get service` komutunu `--watch` bağımsız değişkeniyle birlikte kullanın.

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

![Azure Vote örnek uygulamasına göz atma görüntüsü](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

## <a name="monitor-health-and-logs"></a>Sistem durumunu ve günlükleri izleme

Kümeyi oluşturduğunuzda, kapsayıcılar için Azure Izleyici etkinleştirilmiştir. Kapsayıcılar için Azure izleyici, küme üzerinde çalışan aks kümesi ve pod 'ler için sistem durumu ölçümleri sağlar.

Ölçüm verileri Azure portal doldurmanız birkaç dakika sürer. Azure oyları için geçerli sistem durumu, çalışma süresi ve kaynak kullanımını görmek için:

1. Azure portal AKS kaynağına geri gidin.
1. Sol taraftaki **izleme** altında **Öngörüler**' i seçin.
1. Üst kısmında **+ Filtre Ekle**' yi seçin.
1. Özellik olarak **ad alanı** ' nı seçin ve ardından öğesini seçin *\<All but kube-system\>* .
1. Bunları görüntülemek için **kapsayıcıları** seçin.

`azure-vote-back` `azure-vote-front` Aşağıdaki örnekte gösterildiği gibi, ve kapsayıcıları görüntülenir:

![AKS'de çalışan kapsayıcıların durumunu görüntüleme](media/kubernetes-walkthrough-portal/monitor-containers.png)

Pod 'un günlüklerini görüntülemek için `azure-vote-front` kapsayıcılar listesi açılır listesinden **kapsayıcı günlüklerini görüntüle** ' yi seçin. Bu günlükler, kapsayıcıdaki *stdout* ve *stderr* akışlarını içerir.

![AKS'deki kapsayıcı günlüklerini görüntüleme](media/kubernetes-walkthrough-portal/monitor-container-logs.png)

## <a name="delete-cluster"></a>Kümeyi silme

Azure ücretlerinden kaçınmak için gereksiz kaynaklarınızı temizleyin. AKS kümesi panosunda **Sil** düğmesini seçin. Cloud Shell [az aks Delete][az-aks-delete] komutunu da kullanabilirsiniz:

```azurecli
az aks delete --resource-group myResourceGroup --name myAKSCluster --no-wait
```
> [!NOTE]
> Kümeyi sildiğinizde, AKS kümesi tarafından kullanılan Azure Active Directory hizmet sorumlusu kaldırılmaz. Hizmet sorumlusunu kaldırma adımları için bkz. [AKS hizmet sorumlusuyla ilgili önemli noktalar ve silme][sp-delete].
> 
> Yönetilen bir kimlik kullandıysanız, kimlik platform tarafından yönetilir ve kaldırma gerektirmez.

## <a name="get-the-code"></a>Kodu alma

Önceden var olan kapsayıcı görüntüleri, bu hızlı başlangıçta bir Kubernetes dağıtımı oluşturmak için kullanıldı. İlgili uygulama kodu, Dockerfile ve Kubernetes bildirim dosyası [GitHub ' da kullanılabilir.][azure-vote-app]

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta bir Kubernetes kümesi dağıttınız ve daha sonra çok kapsayıcılı bir uygulama dağıttınız. AKS kümeniz için Kubernetes web panosuna erişin.


Bir uygulama oluşturma, Azure Container Registry dağıtma, çalışan bir uygulamayı güncelleştirme ve kümenizin ölçeklendirilmesi ve yükseltilmesi dahil olmak üzere eksiksiz bir örnek aracılığıyla AKS hakkında daha fazla bilgi edinmek için Kubernetes küme öğreticisine geçin.

> [!div class="nextstepaction"]
> [AKS öğreticisi][aks-tutorial]

<!-- LINKS - external -->
[azure-vote-app]: https://github.com/Azure-Samples/azure-voting-app-redis.git
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-documentation]: https://kubernetes.io/docs/home/

<!-- LINKS - internal -->
[kubernetes-concepts]: concepts-clusters-workloads.md
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-aks-delete]: /cli/azure/aks#az_aks_delete
[aks-monitor]: ../azure-monitor/containers/container-insights-overview.md
[aks-network]: ./concepts-network.md
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[http-routing]: ./http-application-routing.md
[sp-delete]: kubernetes-service-principal.md#additional-considerations