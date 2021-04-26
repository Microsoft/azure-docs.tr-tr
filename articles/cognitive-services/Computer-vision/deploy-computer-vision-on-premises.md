---
title: Kubernetes ve Held ile Görüntü İşleme kapsayıcısı kullanma
titleSuffix: Azure Cognitive Services
description: Kubernetes ve Held kullanarak Görüntü İşleme kapsayıcısını dağıtmayı öğrenin.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 01/27/2020
ms.author: aahi
ms.openlocfilehash: 36091c62814cffd78c5f8132e01820070968af52
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106284957"
---
# <a name="use-computer-vision-container-with-kubernetes-and-helm"></a>Kubernetes ve Held ile Görüntü İşleme kapsayıcısı kullanma

Şirket içi Görüntü İşleme Kapsayıcılarınızı yönetmenin bir seçeneği, Kubernetes ve Held 'yi kullanmaktır. Kubernetes ve Held kullanarak bir Görüntü İşleme kapsayıcı görüntüsü tanımlamak için bir Kubernetes paketi oluşturacağız. Bu paket, şirket içi bir Kubernetes kümesine dağıtılacak. Son olarak, dağıtılan hizmetleri nasıl test ettireceğiz. Kubernetes düzenlemesi olmadan Docker kapsayıcılarını çalıştırma hakkında daha fazla bilgi için bkz. [görüntü işleme kapsayıcıları yükleyip çalıştırma](computer-vision-how-to-install-containers.md).

## <a name="prerequisites"></a>Önkoşullar

Şirket içinde Görüntü İşleme kapsayıcıları kullanmadan önce aşağıdaki Önkoşullar:

| Gerekli | Amaç |
|----------|---------|
| Azure Hesabı | Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap][free-azure-account] oluşturun. |
| Kubernetes CLı | [Kubernetes CLI][kubernetes-cli] , paylaşılan kimlik bilgilerinin kapsayıcı kayıt defterinden yönetilmesi için gereklidir. Kubernetes, Kubernetes paket yöneticisi olan helk 'dan önce de gereklidir. |
| Held CLı | Heln bir grafik (kapsayıcı paket tanımı) yüklemek için kullanılan [Held CLI][helm-install]'yı yükler. |
| Görüntü İşleme kaynağı |Kapsayıcısını kullanabilmeniz için şunları yapmanız gerekir:<br><br>Uç nokta URI 'SI olan bir Azure **görüntü işleme** kaynağı ve ilişkili API anahtarı. Her iki değer de kaynak için genel bakış ve anahtarlar sayfalarında bulunur ve kapsayıcıyı başlatmak için gereklidir.<br><br>**{API_KEY}**: **anahtarlar** sayfasında kullanılabilir iki kaynak anahtardan biri<br><br>**{ENDPOINT_URI}**: **genel bakış** sayfasında belirtilen bitiş noktası|

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>Ana bilgisayar

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Kapsayıcı gereksinimleri ve önerileri

[!INCLUDE [Container requirements and recommendations](includes/container-requirements-and-recommendations.md)]

## <a name="connect-to-the-kubernetes-cluster"></a>Kubernetes kümesine bağlanma

Ana bilgisayarın kullanılabilir bir Kubernetes kümesi olması beklenmektedir. Bir Kubernetes kümesinin bir ana bilgisayara nasıl dağıtılacağını anlamak için bir [Kubernetes kümesi dağıtmaya](../../aks/tutorial-kubernetes-deploy-cluster.md) yönelik Bu öğreticiye bakın. [Kubernetes belgelerindeki](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)dağıtımlar hakkında daha fazla bilgi edinebilirsiniz.

## <a name="configure-helm-chart-values-for-deployment"></a>Dağıtım için Held grafik değerlerini yapılandırma

*Read* adlı bir klasör oluşturarak başlayın. Ardından, aşağıdaki YAML içeriğini adlı yeni bir dosyaya yapıştırın `chart.yaml` :

```yaml
apiVersion: v2
name: read
version: 1.0.0
description: A Helm chart to deploy the Read OCR container to a Kubernetes cluster
dependencies:
- name: rabbitmq
  condition: read.image.args.rabbitmq.enabled
  version: ^6.12.0
  repository: https://kubernetes-charts.storage.googleapis.com/
- name: redis
  condition: read.image.args.redis.enabled
  version: ^6.0.0
  repository: https://kubernetes-charts.storage.googleapis.com/
```

HELI grafiğinin varsayılan değerlerini yapılandırmak için aşağıdaki YAML 'yi kopyalayıp adlı bir dosyaya yapıştırın `values.yaml` . `# {ENDPOINT_URI}`Ve `# {API_KEY}` açıklamalarını kendi değerlerinizle değiştirin. Gerekirse resultExpirationPeriod, Red, ve Kbbitmq 'ı yapılandırın.

```yaml
# These settings are deployment specific and users can provide customizations
read:
  enabled: true
  image:
    name: cognitive-services-read
    registry:  mcr.microsoft.com/
    repository: azure-cognitive-services/vision/read
    tag: 3.2-preview.1
    args:
      eula: accept
      billing: # {ENDPOINT_URI}
      apikey: # {API_KEY}
      
      # Result expiration period setting. Specify when the system should clean up recognition results.
      # For example, resultExpirationPeriod=1, the system will clear the recognition result 1hr after the process.
      # resultExpirationPeriod=0, the system will clear the recognition result after result retrieval.
      resultExpirationPeriod: 1
      
      # Redis storage, if configured, will be used by read OCR container to store result records.
      # A cache is required if multiple read OCR containers are placed behind load balancer.
      redis:
        enabled: false # {true/false}
        password: password

      # RabbitMQ is used for dispatching tasks. This can be useful when multiple read OCR containers are
      # placed behind load balancer.
      rabbitmq:
        enabled: false # {true/false}
        rabbitmq:
          username: user
          password: password
```

> [!IMPORTANT]
> - `billing`Ve `apikey` değerleri sağlanmazsa, hizmetlerin süresi 15 dakika sonra dolacak. Benzer şekilde, hizmetler kullanılamadığından doğrulama başarısız olur.
> 
> - Bir yük dengeleyicinin arkasında birden çok okuma OCR kapsayıcısı dağıtırsanız (örneğin, Docker Compose veya Kubernetes), bir dış önbelleğiniz olması gerekir. İşlem kapsayıcısı ve GET isteği kapsayıcısı aynı olamaz, çünkü bir dış önbellek sonuçları depolar ve kapsayıcılar arasında paylaşır. Önbellek ayarları hakkında daha fazla bilgi için bkz. [görüntü işleme Docker kapsayıcılarını yapılandırma](./computer-vision-resource-container-config.md).
>

*Okuma* dizininin altında bir *Şablonlar* klasörü oluşturun. Aşağıdaki YAML 'yi kopyalayıp adlı bir dosyaya yapıştırın `deployment.yaml` . `deployment.yaml`Dosya, helb şablonu olarak görev yapar.

> Şablonlar, Kubernetes 'in anlayabileceği YAML biçimli kaynak açıklamaları olan bildirim dosyaları oluşturur. [-Help grafik şablonu Kılavuzu][chart-template-guide]

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: read
  labels:
    app: read-deployment
spec:
  selector:
    matchLabels:
      app: read-app
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
        args:        
        - ReadEngineConfig:ResultExpirationPeriod={{ .Values.read.image.args.resultExpirationPeriod }}
        {{- if .Values.read.image.args.rabbitmq.enabled }}
        - Queue:RabbitMQ:HostName={{ include "rabbitmq.hostname" . }}
        - Queue:RabbitMQ:Username={{ .Values.read.image.args.rabbitmq.rabbitmq.username }}
        - Queue:RabbitMQ:Password={{ .Values.read.image.args.rabbitmq.rabbitmq.password }}
        {{- end }}      
        {{- if .Values.read.image.args.redis.enabled }}
        - Cache:Redis:Configuration={{ include "redis.connStr" . }}
        {{- end }}
      imagePullSecrets:
      - name: {{.Values.read.image.pullSecret}}      
--- 
apiVersion: v1
kind: Service
metadata:
  name: read-service
spec:
  type: LoadBalancer
  ports:
  - port: 5000
  selector:
    app: read-app
```

Aynı *Şablonlar* klasöründe aşağıdaki yardımcı işlevleri kopyalayıp içine yapıştırın `helpers.tpl` . `helpers.tpl` Hele şablonu oluşturmaya yardımcı olmak için yararlı işlevler tanımlar.

> [!NOTE]
> Bu makale, Microsoft 'un artık kullandığı bir terim olan bağımlı dönem başvuruları içerir. Terim yazılımlardan kaldırıldığında, bu makaleden kaldıracağız.

```yaml
{{- define "rabbitmq.hostname" -}}
{{- printf "%s-rabbitmq" .Release.Name -}}
{{- end -}}

{{- define "redis.connStr" -}}
{{- $hostMain := printf "%s-redis-master:6379" .Release.Name }}
{{- $hostReplica := printf "%s-redis-slave:6379" .Release.Name -}}
{{- $passWord := printf "password=%s" .Values.read.image.args.redis.password -}}
{{- $connTail := "ssl=False,abortConnect=False" -}}
{{- printf "%s,%s,%s,%s" $hostMain $hostReplica $passWord $connTail -}}
{{- end -}}
```
Şablon, bir yük dengeleyici hizmetini ve okuma için kapsayıcının/görüntünüzün dağıtımını belirtir.

### <a name="the-kubernetes-package-helm-chart"></a>Kubernetes paketi (helk grafiği)

*Helk grafiği* , kapsayıcı kayıt defterinden hangi Docker görüntüsünün çekeceğini tanımlayan yapılandırmayı içerir `mcr.microsoft.com` .

> [Helk grafiği][helm-charts] , Ilgili bir Kubernetes kaynakları kümesini tanımlayan bir dosya koleksiyonudur. Tek bir grafik, bir veya daha çok karmaşık, örneğin, HTTP sunucuları, veritabanları, önbellekler gibi tam bir Web uygulaması yığını gibi basit bir şeyi dağıtmak için kullanılabilir.

Belirtilen *HELI grafikleri* , görüntü işleme hizmetinin Docker görüntülerini ve `mcr.microsoft.com` kapsayıcı kayıt defterinden karşılık gelen hizmeti çeker.

## <a name="install-the-helm-chart-on-the-kubernetes-cluster"></a>Kubernetes kümesine helk grafiğini yükler

*Helk grafiğini* yüklemek için komutunu yürütmemiz gerekir [`helm install`][helm-install-cmd] . Klasörü yukarıdaki dizinden install komutunu yürütdiğinizden emin olun `read` .

```console
helm install read ./read
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

## <a name="deploy-multiple-v3-containers-on-the-kubernetes-cluster"></a>Kubernetes kümesinde birden çok v3 kapsayıcısı dağıtma

Kapsayıcının v3 sürümünde başlayarak, kapsayıcıları hem görev hem de sayfa düzeyinde paralel olarak kullanabilirsiniz.

Tasarıma göre, her v3 kapsayıcısının bir dağıtıcı ve bir tanıma çalışanı vardır. Dağıtıcı çok sayfalı bir görevi birden çok tek sayfalı alt görevlere bölmekten sorumludur. Tanınma çalışanı tek sayfalı bir belgeyi tanıması için iyileştirilmiştir. Sayfa düzeyinde paralellik sağlamak için, bir yük dengeleyicinin arkasında birden çok v3 kapsayıcısı dağıtın ve kapsayıcıların bir evrensel depolama ve kuyruk paylaşmasına izin verin. 

> [!NOTE] 
> Şu anda yalnızca Azure depolama ve Azure kuyruğu desteklenmektedir. 

İsteği alan kapsayıcı, görevi tek sayfalı alt görevlere bölebilir ve bunları evrensel kuyruğa ekleyebilir. Daha az meşgul bir kapsayıcıdan yapılan herhangi bir tanınma çalışanı kuyruktan tek sayfalı alt görevler tüketebilir, tanıma gerçekleştirebilir ve sonucu depolamaya yükleyebilir. Aktarım hızı, `n` dağıtılan kapsayıcıların sayısına bağlı olarak en çok iyileştirilen olabilir.

V3 kapsayıcısı yolun altındaki lizleştirme araştırma API 'sini kullanıma sunar `/ContainerLiveness` . Kubernetes için bir lizleştirme araştırması yapılandırmak üzere aşağıdaki dağıtım örneğini kullanın. 

Aşağıdaki YAML 'yi kopyalayıp adlı bir dosyaya yapıştırın `deployment.yaml` . `# {ENDPOINT_URI}`Ve `# {API_KEY}` açıklamalarını kendi değerlerinizle değiştirin. `# {AZURE_STORAGE_CONNECTION_STRING}`Yorumu Azure Storage bağlantı dizeniz ile değiştirin. `replicas`Aşağıdaki örnekte olarak ayarlanmış istediğiniz sayıya yapılandırın `3` .

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: read
  labels:
    app: read-deployment
spec:
  selector:
    matchLabels:
      app: read-app
  replicas: # {NUMBER_OF_READ_CONTAINERS}
  template:
    metadata:
      labels:
        app: read-app
    spec:
      containers:
      - name: cognitive-services-read
        image: mcr.microsoft.com/azure-cognitive-services/vision/read
        ports:
        - containerPort: 5000
        env:
        - name: EULA
          value: accept
        - name: billing
          value: # {ENDPOINT_URI}
        - name: apikey
          value: # {API_KEY}
        - name: Storage__ObjectStore__AzureBlob__ConnectionString
          value: # {AZURE_STORAGE_CONNECTION_STRING}
        - name: Queue__Azure__ConnectionString
          value: # {AZURE_STORAGE_CONNECTION_STRING}
        livenessProbe:
          httpGet:
            path: /ContainerLiveness
            port: 5000
          initialDelaySeconds: 60
          periodSeconds: 60
          timeoutSeconds: 20
--- 
apiVersion: v1
kind: Service
metadata:
  name: azure-cognitive-service-read
spec:
  type: LoadBalancer
  ports:
  - port: 5000
    targetPort: 5000
  selector:
    app: read-app
```

Aşağıdaki komutu çalıştırın. 

```console
kubectl apply -f deployment.yaml
```

Aşağıda, başarılı bir dağıtım yürütmesinden görebileceğiniz bir örnek çıktı verilmiştir:

```console
deployment.apps/read created
service/azure-cognitive-service-read created
```

Kubernetes dağıtımının tamamlanması birkaç dakika sürebilir. Hem yığınların hem de hizmetlerin düzgün bir şekilde dağıtıldığını ve kullanılabilir olduğunu onaylamak için aşağıdaki komutu yürütün:

```console
kubectl get all
```

Konsol çıkışını aşağıdakine benzer şekilde görmeniz gerekir:

```console
kubectl get all
NAME                       READY   STATUS    RESTARTS   AGE
pod/read-6cbbb6678-58s9t   1/1     Running   0          3s
pod/read-6cbbb6678-kz7v4   1/1     Running   0          3s
pod/read-6cbbb6678-s2pct   1/1     Running   0          3s

NAME                                   TYPE           CLUSTER-IP   EXTERNAL-IP    PORT(S)          AGE
service/azure-cognitive-service-read   LoadBalancer   10.0.134.0   <none>         5000:30846/TCP   17h
service/kubernetes                     ClusterIP      10.0.0.1     <none>         443/TCP          78d

NAME                   READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/read   3/3     3            3           3s

NAME                             DESIRED   CURRENT   READY   AGE
replicaset.apps/read-6cbbb6678   3         3         3       3s
```

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="next-steps"></a>Sonraki adımlar

Azure Kubernetes Service (AKS) ' de Held ile uygulama yükleme hakkında daha fazla bilgi için, [buraya gidin][installing-helm-apps-in-aks].

> [!div class="nextstepaction"]
> [Bilişsel Hizmetler Kapsayıcıları][cog-svcs-containers]

<!-- LINKS - external -->
[free-azure-account]: https://azure.microsoft.com/free
[git-download]: https://git-scm.com/downloads
[azure-cli]: /cli/azure/install-azure-cli
[docker-engine]: https://www.docker.com/products/docker-engine
[kubernetes-cli]: https://kubernetes.io/docs/tasks/tools/install-kubectl
[helm-install]: https://helm.sh/docs/using_helm/#installing-helm
[helm-install-cmd]: https://helm.sh/docs/intro/using_helm/#helm-install-installing-a-package
[helm-charts]: https://helm.sh/docs/topics/charts/
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[helm-test]: https://helm.sh/docs/helm/#helm-test
[chart-template-guide]: https://helm.sh/docs/chart_template_guide

<!-- LINKS - internal -->
[vision-container-host-computer]: computer-vision-how-to-install-containers.md#the-host-computer
[installing-helm-apps-in-aks]: ../../aks/kubernetes-helm.md
[cog-svcs-containers]: ../cognitive-services-container-support.md
