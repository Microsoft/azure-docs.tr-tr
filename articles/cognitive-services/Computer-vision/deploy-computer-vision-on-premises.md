---
title: Kubernetes ve Held ile Görüntü İşleme kapsayıcısı kullanma
titleSuffix: Azure Cognitive Services
description: Görüntü İşleme kapsayıcısını bir Azure Container Instance 'a dağıtın ve bir Web tarayıcısında test edin.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: 8d285bf60e356f15caf55271b0791e9adc97ac14
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73481759"
---
# <a name="use-computer-vision-container-with-kubernetes-and-helm"></a>Kubernetes ve Held ile Görüntü İşleme kapsayıcısı kullanma

Şirket içi Görüntü İşleme Kapsayıcılarınızı yönetmenin bir seçeneği, Kubernetes ve Held 'yi kullanmaktır. Kubernetes ve Held kullanarak bir Görüntü İşleme kapsayıcı görüntüsü tanımlamak için bir Kubernetes paketi oluşturacağız. Bu paket, şirket içi bir Kubernetes kümesine dağıtılacak. Son olarak, dağıtılan hizmetleri nasıl test ettireceğiz. Kubernetes düzenlemesi olmadan Docker kapsayıcılarını çalıştırma hakkında daha fazla bilgi için bkz. [görüntü işleme kapsayıcıları yükleyip çalıştırma](computer-vision-how-to-install-containers.md).

## <a name="prerequisites"></a>Önkoşullar

Şirket içinde Görüntü İşleme kapsayıcıları kullanmadan önce aşağıdaki Önkoşullar:

|Gerekli|Amaç|
|--|--|
| Azure hesabı | Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap][free-azure-account] oluşturun. |
| Kubernetes CLı | [Kubernetes CLI][kubernetes-cli] , paylaşılan kimlik bilgilerinin kapsayıcı kayıt defterinden yönetilmesi için gereklidir. Kubernetes, Kubernetes paket yöneticisi olan helk 'dan önce de gereklidir. |
| Held CLı | [HELı CLI][helm-install] yüklemesinin bir parçası olarak, [Tiller][tiller-install]yükleyecek Held 'yi de başlatmalısınız. |
| Görüntü İşleme kaynağı |Kapsayıcısını kullanabilmeniz için şunları yapmanız gerekir:<br><br>Uç nokta URI 'SI olan bir Azure **görüntü işleme** kaynağı ve ilişkili API anahtarı. Her iki değer de kaynak için genel bakış ve anahtarlar sayfalarında bulunur ve kapsayıcıyı başlatmak için gereklidir.<br><br>**{API_KEY}** : **anahtarlar** sayfasında kullanılabilir iki kaynak anahtardan biri<br><br>**{ENDPOINT_URI}** : **genel bakış** sayfasında belirtilen bitiş noktası|

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>Ana bilgisayar

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Kapsayıcı gereksinimleri ve önerileri

[!INCLUDE [Container requirements and recommendations](includes/container-requirements-and-recommendations.md)]

## <a name="connect-to-the-kubernetes-cluster"></a>Kubernetes kümesine bağlanma

Ana bilgisayarın kullanılabilir bir Kubernetes kümesi olması beklenmektedir. Bir Kubernetes kümesinin bir ana bilgisayara nasıl dağıtılacağını anlamak için bir [Kubernetes kümesi dağıtmaya](../../aks/tutorial-kubernetes-deploy-cluster.md) yönelik Bu öğreticiye bakın.

### <a name="sharing-docker-credentials-with-the-kubernetes-cluster"></a>Docker kimlik bilgilerini Kubernetes kümesiyle paylaşma

Kubernetes kümesinin, `containerpreview.azurecr.io` Container kayıt defterinden yapılandırılan resimleri `docker pull` izin vermek için, Docker kimlik bilgilerini kümeye aktarmanız gerekir. Kapsayıcı kayıt defteri erişim önkoşulu ' ndan belirtilen kimlik bilgilerine göre *Docker-Registry gizli* anahtarı oluşturmak için aşağıdaki [`kubectl create`][kubectl-create] komutunu yürütün.

Seçtiğiniz komut satırı arabiriminizden aşağıdaki komutu çalıştırın. `<username>`, `<password>`ve `<email-address>` kapsayıcı kayıt defteri kimlik bilgileriyle değiştirdiğinizden emin olun.

```console
kubectl create secret docker-registry containerpreview \
    --docker-server=containerpreview.azurecr.io \
    --docker-username=<username> \
    --docker-password=<password> \
    --docker-email=<email-address>
```

> [!NOTE]
> `containerpreview.azurecr.io` kapsayıcı kayıt defterine zaten erişiminiz varsa, bunun yerine genel bayrağını kullanarak bir Kubernetes gizli dizisi oluşturabilirsiniz. Docker yapılandırmanızda JSON olarak çalıştırılan aşağıdaki komutu göz önünde bulundurun.
> ```console
>  kubectl create secret generic containerpreview \
>      --from-file=.dockerconfigjson=~/.docker/config.json \
>      --type=kubernetes.io/dockerconfigjson
> ```

Gizli dizi başarıyla oluşturulduğunda konsola aşağıdaki çıktı yazdırılır.

```console
secret "containerpreview" created
```

Gizli dizinin oluşturulduğunu doğrulamak için, [`kubectl get`][kubectl-get] `secrets` bayrağıyla yürütün.

```console
kuberctl get secrets
```

`kubectl get secrets` yürütmek, yapılandırılan tüm gizli dizileri yazdırır.

```console
NAME                  TYPE                                  DATA      AGE
containerpreview      kubernetes.io/dockerconfigjson        1         30s
```

## <a name="configure-helm-chart-values-for-deployment"></a>Dağıtım için Held grafik değerlerini yapılandırma

*Oku*adlı bir klasör oluşturarak başlayın, ardından aşağıdaki YAML içeriğini *Chart. yıml*adlı yeni bir dosyaya yapıştırın.

```yaml
apiVersion: v1
name: read
version: 1.0.0
description: A Helm chart to deploy the microsoft/cognitive-services-read to a Kubernetes cluster
```

HELI grafiğinin varsayılan değerlerini yapılandırmak için aşağıdaki YAML 'yi kopyalayıp `values.yaml`adlı bir dosyaya yapıştırın. `# {ENDPOINT_URI}` ve `# {API_KEY}` açıklamalarını kendi değerlerinizle değiştirin.

```yaml
# These settings are deployment specific and users can provide customizations

read:
  enabled: true
  image:
    name: cognitive-services-read
    registry: containerpreview.azurecr.io/
    repository: microsoft/cognitive-services-read
    tag: latest
    pullSecret: containerpreview # Or an existing secret
    args:
      eula: accept
      billing: # {ENDPOINT_URI}
      apikey: # {API_KEY}
```

> [!IMPORTANT]
> `billing` ve `apikey` değerleri sağlanmazsa, hizmetlerin süreleri 15 dakikadan sonra dolacak. Benzer şekilde, hizmetler kullanılamadığından doğrulama başarısız olur.

*Okuma* dizininin altında bir *Şablonlar* klasörü oluşturun. Aşağıdaki YAML 'yi kopyalayıp `deployment.yaml`adlı bir dosyaya yapıştırın. `deployment.yaml` dosyası bir hela şablonu olarak görev yapar.

> Şablonlar, Kubernetes 'in anlayabileceği YAML biçimli kaynak açıklamaları olan bildirim dosyaları oluşturur. [-Help grafik şablonu Kılavuzu][chart-template-guide]

```yaml
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: read
spec:
  template:
    metadata:
      labels:
        app: read-app
    spec:
      containers:
      - name: {{.Values.read.image.name}}
        image: {{.Values.read.image.registry}}{{.Values.read.image.repository}}
        ports:
        - containerPort: 5000
        env:
        - name: EULA
          value: {{.Values.read.image.args.eula}}
        - name: billing
          value: {{.Values.read.image.args.billing}}
        - name: apikey
          value: {{.Values.read.image.args.apikey}}
      imagePullSecrets:
      - name: {{.Values.read.image.pullSecret}}

--- 
apiVersion: v1
kind: Service
metadata:
  name: read
spec:
  type: LoadBalancer
  ports:
  - port: 5000
  selector:
    app: read-app
```

Şablon, bir yük dengeleyici hizmetini ve okuma için kapsayıcının/görüntünüzün dağıtımını belirtir.

### <a name="the-kubernetes-package-helm-chart"></a>Kubernetes paketi (helk grafiği)

*Helk grafiği* `containerpreview.azurecr.io` kapsayıcı kayıt defterinden hangi Docker görüntüsünün çekeceğini yapılandırmayı içerir.

> [Helk grafiği][helm-charts] , Ilgili bir Kubernetes kaynakları kümesini tanımlayan bir dosya koleksiyonudur. Tek bir grafik, bir veya daha çok karmaşık, örneğin, HTTP sunucuları, veritabanları, önbellekler gibi tam bir Web uygulaması yığını gibi basit bir şeyi dağıtmak için kullanılabilir.

Belirtilen *HELI grafikleri* , görüntü işleme hizmetinin Docker görüntülerini ve `containerpreview.azurecr.io` Container kayıt defterinden karşılık gelen hizmeti çeker.

## <a name="install-the-helm-chart-on-the-kubernetes-cluster"></a>Kubernetes kümesine helk grafiğini yükler

*Helk grafiğini*yüklemek için [`helm install`][helm-install-cmd] komutunu yürütmemiz gerekir. `read` klasörünün üzerindeki dizinden install komutunu yürütdiğinizden emin olun.

```console
helm install read --name read
```

İşte başarılı bir yüklemenin yürütülmesini beklemeniz gerekebilecek bir örnek çıktı:

```console
NAME: read
LAST DEPLOYED: Thu Sep 04 13:24:06 2019
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Pod(related)
NAME                    READY  STATUS             RESTARTS  AGE
read-57cb76bcf7-45sdh   0/1    ContainerCreating  0         0s

==> v1/Service
NAME     TYPE          CLUSTER-IP    EXTERNAL-IP  PORT(S)         AGE
read     LoadBalancer  10.110.44.86  localhost    5000:31301/TCP  0s

==> v1beta1/Deployment
NAME    READY  UP-TO-DATE  AVAILABLE  AGE
read    0/1    1           0          0s
```

Kubernetes dağıtımının tamamlanması birkaç dakika sürebilir. Hem yığınların hem de hizmetlerin düzgün bir şekilde dağıtıldığını ve kullanılabilir olduğunu onaylamak için aşağıdaki komutu yürütün:

```console
kubectl get all
```

Aşağıdaki çıktıya benzer bir şey görmeniz beklenir:

```console
kubectl get all
NAME                        READY   STATUS    RESTARTS   AGE
pod/read-57cb76bcf7-45sdh   1/1     Running   0          17s

NAME                   TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
service/kubernetes     ClusterIP      10.96.0.1      <none>        443/TCP          45h
service/read           LoadBalancer   10.110.44.86   localhost     5000:31301/TCP   17s

NAME                   READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/read   1/1     1            1           17s

NAME                              DESIRED   CURRENT   READY   AGE
replicaset.apps/read-57cb76bcf7   1         1         1       17s
```
<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="next-steps"></a>Sonraki adımlar

Azure Kubernetes Service (AKS) ' de Held ile uygulama yükleme hakkında daha fazla bilgi için, [buraya gidin][installing-helm-apps-in-aks].

> [!div class="nextstepaction"]
> [Bilişsel hizmetler kapsayıcıları][cog-svcs-containers]

<!-- LINKS - external -->
[free-azure-account]: https://azure.microsoft.com/free
[git-download]: https://git-scm.com/downloads
[azure-cli]: https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest
[docker-engine]: https://www.docker.com/products/docker-engine
[kubernetes-cli]: https://kubernetes.io/docs/tasks/tools/install-kubectl
[helm-install]: https://helm.sh/docs/using_helm/#installing-helm
[helm-install-cmd]: https://helm.sh/docs/helm/#helm-install
[tiller-install]: https://helm.sh/docs/install/#installing-tiller
[helm-charts]: https://helm.sh/docs/developing_charts
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[helm-test]: https://helm.sh/docs/helm/#helm-test
[chart-template-guide]: https://helm.sh/docs/chart_template_guide

<!-- LINKS - internal -->
[vision-container-host-computer]: computer-vision-how-to-install-containers.md#the-host-computer
[installing-helm-apps-in-aks]: ../../aks/kubernetes-helm.md
[cog-svcs-containers]: ../cognitive-services-container-support.md
