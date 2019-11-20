---
title: Kubernetes ve Held ile konuşma hizmeti kapsayıcısını kullanma
titleSuffix: Azure Cognitive Services
description: Kubernetes ve helk kullanarak konuşmadan metne ve metinden konuşmaya kapsayıcı görüntülerini tanımlamak için bir Kubernetes paketi oluşturacağız. Bu paket, şirket içi bir Kubernetes kümesine dağıtılacak.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: b413bc6d29f1b08949b50570cb5baa2eb758d779
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73491017"
---
# <a name="use-speech-service-container-with-kubernetes-and-helm"></a>Kubernetes ve Held ile konuşma hizmeti kapsayıcısını kullanma

Şirket içi konuşma kapsayılarınızı yönetmeye yönelik bir seçenek de Kubernetes ve Held 'yi kullanmaktır. Kubernetes ve helk kullanarak konuşmadan metne ve metinden konuşmaya kapsayıcı görüntülerini tanımlamak için bir Kubernetes paketi oluşturacağız. Bu paket, şirket içi bir Kubernetes kümesine dağıtılacak. Son olarak, dağıtılan Hizmetleri ve çeşitli yapılandırma seçeneklerini nasıl test eteceksiniz. Kubernetes düzenlemesi olmadan Docker kapsayıcılarını çalıştırma hakkında daha fazla bilgi için bkz. [konuşma hizmeti kapsayıcıları 'nı yükleyip çalıştırma](speech-container-howto.md).

## <a name="prerequisites"></a>Önkoşullar

Şirket içi konuşma kapsayıcılarını kullanmadan önce aşağıdaki Önkoşullar:

|Gerekli|Amaç|
|--|--|
| Azure hesabı | Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap][free-azure-account] oluşturun. |
| Container Registry erişim | Kubernetes 'in, Docker görüntülerini kümeye çekmesi için kapsayıcı kayıt defterine erişmesi gerekir. |
| Kubernetes CLı | [Kubernetes CLI][kubernetes-cli] , paylaşılan kimlik bilgilerinin kapsayıcı kayıt defterinden yönetilmesi için gereklidir. Kubernetes, Kubernetes paket yöneticisi olan helk 'dan önce de gereklidir. |
| Held CLı | [HELı CLI][helm-install] yüklemesinin bir parçası olarak, [Tiller][tiller-install]yükleyecek Held 'yi de başlatmalısınız. |
|Konuşma kaynağı |Bu kapsayıcıları kullanabilmeniz için, şunları yapmanız gerekir:<br><br>İlgili fatura anahtarını ve faturalama uç noktası URI 'sini almak için bir _konuşma_ Azure kaynağı. Her iki değer de Azure portal **konuşmaya** genel bakış ve anahtarlar sayfalarında bulunur ve kapsayıcıyı başlatmak için gereklidir.<br><br>**{API_KEY}** : kaynak anahtarı<br><br>**{ENDPOINT_URI}** : uç nokta URI örneği: `https://westus.api.cognitive.microsoft.com/sts/v1.0`|

## <a name="the-recommended-host-computer-configuration"></a>Önerilen Konak bilgisayar yapılandırması

Bir başvuru olarak [konuşma hizmeti kapsayıcı ana bilgisayar][speech-container-host-computer] ayrıntılarına bakın. Bu *helb grafiği* , kullanıcının belirttiği kaç tane kodunu çözme (eşzamanlı istek) temelinde CPU ve bellek gereksinimlerini otomatik olarak hesaplar. Ayrıca, ses/metin girişine yönelik iyileştirmelerin `enabled`olarak yapılandırılıp yapılandırılmadığını temel alır. HELI grafiğinin varsayılan değeri, iki eşzamanlı istek ve en iyi duruma getirme.

| Hizmet | CPU/kapsayıcı | Bellek/kapsayıcı |
|--|--|--|
| **Konuşmayı metne dönüştürme** | bir kod çözücü en az 1.150 milimetre çekirdek gerektirir. `optimizedForAudioFile` etkinleştirilirse, 1.950 miliçekirdekler gereklidir. (varsayılan: iki kod çözücü) | Gerekli: 2 GB<br>Sınırlı: 4 GB |
| **Metin okuma** | bir eşzamanlı istek en az 500 milicore gerektirir. `optimizeForTurboMode` etkinleştirilirse, 1.000 miliçekirdekler gereklidir. (varsayılan: iki eşzamanlı istek) | Gerekli: 1 GB<br> Sınırlı: 2 GB |

## <a name="connect-to-the-kubernetes-cluster"></a>Kubernetes kümesine bağlanma

Ana bilgisayarın kullanılabilir bir Kubernetes kümesi olması beklenmektedir. Bir Kubernetes kümesinin bir ana bilgisayara nasıl dağıtılacağını anlamak için bir [Kubernetes kümesi dağıtmaya](../../aks/tutorial-kubernetes-deploy-cluster.md) yönelik Bu öğreticiye bakın.

### <a name="sharing-docker-credentials-with-the-kubernetes-cluster"></a>Docker kimlik bilgilerini Kubernetes kümesiyle paylaşma

Kubernetes kümesinin, `mcr.microsoft.com` Container kayıt defterinden yapılandırılan resimleri `docker pull` izin vermek için, Docker kimlik bilgilerini kümeye aktarmanız gerekir. Kapsayıcı kayıt defteri erişim önkoşulu ' ndan belirtilen kimlik bilgilerine göre *Docker-Registry gizli* anahtarı oluşturmak için aşağıdaki [`kubectl create`][kubectl-create] komutunu yürütün.

Seçtiğiniz komut satırı arabiriminizden aşağıdaki komutu çalıştırın. `<username>`, `<password>`ve `<email-address>` kapsayıcı kayıt defteri kimlik bilgileriyle değiştirdiğinizden emin olun.

```console
kubectl create secret docker-registry mcr \
    --docker-server=mcr.microsoft.com \
    --docker-username=<username> \
    --docker-password=<password> \
    --docker-email=<email-address>
```

> [!NOTE]
> `mcr.microsoft.com` kapsayıcı kayıt defterine zaten erişiminiz varsa, bunun yerine genel bayrağını kullanarak bir Kubernetes gizli dizisi oluşturabilirsiniz. Docker yapılandırmanızda JSON olarak çalıştırılan aşağıdaki komutu göz önünde bulundurun.
> ```console
>  kubectl create secret generic mcr \
>      --from-file=.dockerconfigjson=~/.docker/config.json \
>      --type=kubernetes.io/dockerconfigjson
> ```

Gizli dizi başarıyla oluşturulduğunda konsola aşağıdaki çıktı yazdırılır.

```console
secret "mcr" created
```

Gizli dizinin oluşturulduğunu doğrulamak için, [`kubectl get`][kubectl-get] `secrets` bayrağıyla yürütün.

```console
kuberctl get secrets
```

`kubectl get secrets` yürütmek, yapılandırılan tüm gizli dizileri yazdırır.

```console
NAME    TYPE                              DATA    AGE
mcr     kubernetes.io/dockerconfigjson    1       30s
```

## <a name="configure-helm-chart-values-for-deployment"></a>Dağıtım için Held grafik değerlerini yapılandırma

Microsoft tarafından sunulan tüm genel kullanıma açık HELI grafikleri için [Microsoft HELI hub 'ını][ms-helm-hub] ziyaret edin. Microsoft Held hub 'ında bilişsel **Hizmetler konuşma şirket Içi grafiğini**bulacaksınız. Bilişsel **Hizmetler konuşma şirket içi** , yükleytiğimiz grafiktir, ancak önce açık yapılandırmalara sahip bir `config-values.yaml` dosyası oluşturulmalıdır. Helm örneğimize Microsoft deposunu ekleyerek başlayalım.

```console
helm repo add microsoft https://microsoft.github.io/charts/repo
```

Sonra, helk grafik değerlerimizi yapılandıracağız. Aşağıdaki YAML 'yi kopyalayıp `config-values.yaml`adlı bir dosyaya yapıştırın. Bilişsel **Hizmetler konuşma şirket Içi HELI grafiğini**özelleştirme hakkında daha fazla bilgi için bkz. [HELI grafiklerini özelleştirme](#customize-helm-charts). `# {ENDPOINT_URI}` ve `# {API_KEY}` açıklamalarını kendi değerlerinizle değiştirin.

```yaml
# These settings are deployment specific and users can provide customizations

# speech-to-text configurations
speechToText:
  enabled: true
  numberOfConcurrentRequest: 3
  optimizeForAudioFile: true
  image:
    registry: mcr.microsoft.com
    repository: azure-cognitive-services/speech-to-text
    tag: latest
    pullSecrets:
      - mcr # Or an existing secret
    args:
      eula: accept
      billing: # {ENDPOINT_URI}
      apikey: # {API_KEY}

# text-to-speech configurations
textToSpeech:
  enabled: true
  numberOfConcurrentRequest: 3
  optimizeForTurboMode: true
  image:
    registry: mcr.microsoft.com
    repository: azure-cognitive-services/text-to-speech
    tag: latest
    pullSecrets:
      - mcr # Or an existing secret
    args:
      eula: accept
      billing: # {ENDPOINT_URI}
      apikey: # {API_KEY}
```

> [!IMPORTANT]
> `billing` ve `apikey` değerleri sağlanmazsa, hizmetlerin süreleri 15 dakikadan sonra dolacak. Benzer şekilde, hizmetler kullanılamadığından doğrulama başarısız olur.

### <a name="the-kubernetes-package-helm-chart"></a>Kubernetes paketi (helk grafiği)

*Helk grafiği* `mcr.microsoft.com` kapsayıcı kayıt defterinden hangi Docker görüntüsünün çekeceğini yapılandırmayı içerir.

> [Helk grafiği][helm-charts] , Ilgili bir Kubernetes kaynakları kümesini tanımlayan bir dosya koleksiyonudur. Tek bir grafik, bir veya daha çok karmaşık, örneğin, HTTP sunucuları, veritabanları, önbellekler gibi tam bir Web uygulaması yığını gibi basit bir şeyi dağıtmak için kullanılabilir.

Sunulan *Helu grafikleri* , konuşma hizmetinin Docker görüntülerini, hem metinden konuşmaya hem de `mcr.microsoft.com` kapsayıcı kayıt defterinden konuşmaya metin hizmetlerini çekmesini sağlar.

## <a name="install-the-helm-chart-on-the-kubernetes-cluster"></a>Kubernetes kümesine helk grafiğini yükler

*Helk grafiğini* yüklemek için, `<config-values.yaml>` uygun yol ve dosya adı bağımsız değişkeniyle değiştirerek [`helm install`][helm-install-cmd] komutunu yürütmemiz gerekir. Aşağıda başvurulan `microsoft/cognitive-services-speech-onpremise` Helu grafiği [burada Microsoft Held hub 'ında][ms-helm-hub-speech-chart]bulunabilir.

```console
helm install microsoft/cognitive-services-speech-onpremise \
    --version 0.1.1 \
    --values <config-values.yaml> \
    --name onprem-speech
```

İşte başarılı bir yüklemenin yürütülmesini beklemeniz gerekebilecek bir örnek çıktı:

```console
NAME:   onprem-speech
LAST DEPLOYED: Tue Jul  2 12:51:42 2019
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Pod(related)
NAME                             READY  STATUS             RESTARTS  AGE
speech-to-text-7664f5f465-87w2d  0/1    Pending            0         0s
speech-to-text-7664f5f465-klbr8  0/1    ContainerCreating  0         0s
text-to-speech-56f8fb685b-4jtzh  0/1    ContainerCreating  0         0s
text-to-speech-56f8fb685b-frwxf  0/1    Pending            0         0s

==> v1/Service
NAME            TYPE          CLUSTER-IP    EXTERNAL-IP  PORT(S)       AGE
speech-to-text  LoadBalancer  10.0.252.106  <pending>    80:31811/TCP  1s
text-to-speech  LoadBalancer  10.0.125.187  <pending>    80:31247/TCP  0s

==> v1beta1/PodDisruptionBudget
NAME                                MIN AVAILABLE  MAX UNAVAILABLE  ALLOWED DISRUPTIONS  AGE
speech-to-text-poddisruptionbudget  N/A            20%              0                    1s
text-to-speech-poddisruptionbudget  N/A            20%              0                    1s

==> v1beta2/Deployment
NAME            READY  UP-TO-DATE  AVAILABLE  AGE
speech-to-text  0/2    2           0          0s
text-to-speech  0/2    2           0          0s

==> v2beta2/HorizontalPodAutoscaler
NAME                       REFERENCE                  TARGETS        MINPODS  MAXPODS  REPLICAS  AGE
speech-to-text-autoscaler  Deployment/speech-to-text  <unknown>/50%  2        10       0         0s
text-to-speech-autoscaler  Deployment/text-to-speech  <unknown>/50%  2        10       0         0s


NOTES:
cognitive-services-speech-onpremise has been installed!
Release is named onprem-speech
```

Kubernetes dağıtımının tamamlanması birkaç dakika sürebilir. Hem yığınların hem de hizmetlerin düzgün bir şekilde dağıtıldığını ve kullanılabilir olduğunu onaylamak için aşağıdaki komutu yürütün:

```console
kubectl get all
```

Aşağıdaki çıktıya benzer bir şey görmeniz beklenir:

```console
NAME                                  READY     STATUS    RESTARTS   AGE
pod/speech-to-text-7664f5f465-87w2d   1/1       Running   0          34m
pod/speech-to-text-7664f5f465-klbr8   1/1       Running   0          34m
pod/text-to-speech-56f8fb685b-4jtzh   1/1       Running   0          34m
pod/text-to-speech-56f8fb685b-frwxf   1/1       Running   0          34m

NAME                     TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)        AGE
service/kubernetes       ClusterIP      10.0.0.1       <none>           443/TCP        3h
service/speech-to-text   LoadBalancer   10.0.252.106   52.162.123.151   80:31811/TCP   34m
service/text-to-speech   LoadBalancer   10.0.125.187   65.52.233.162    80:31247/TCP   34m

NAME                             DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/speech-to-text   2         2         2            2           34m
deployment.apps/text-to-speech   2         2         2            2           34m

NAME                                        DESIRED   CURRENT   READY     AGE
replicaset.apps/speech-to-text-7664f5f465   2         2         2         34m
replicaset.apps/text-to-speech-56f8fb685b   2         2         2         34m

NAME                                                            REFERENCE                   TARGETS   MINPODS   MAXPODS   REPLICAS   AGE
horizontalpodautoscaler.autoscaling/speech-to-text-autoscaler   Deployment/speech-to-text   1%/50%    2         10        2          34m
horizontalpodautoscaler.autoscaling/text-to-speech-autoscaler   Deployment/text-to-speech   0%/50%    2         10        2          34m
```

### <a name="verify-helm-deployment-with-helm-tests"></a>Held testleriyle Held dağıtımını doğrulama

Yüklü HELI grafikleri, doğrulama için kolaylık olarak görev sunan *helk testlerini*tanımlar. Bu sınamalar hizmet hazırlığını doğrular. Hem **konuşmadan metne** hem de **metinden konuşmaya** hizmetlerini doğrulamak için [helk test][helm-test] komutunu yürüteceğiz.

```console
helm test onprem-speech
```

> [!IMPORTANT]
> POD durumu `Running` yoksa ve dağıtım `AVAILABLE` sütununun altında listelenmiyorsa, bu testler başarısız olur. Bunun tamamlanması on dakikadan uzun sürebilmesi için sabırlı olun.

Bu testler çeşitli durum sonuçlarının çıktısını alacak:

```console
RUNNING: speech-to-text-readiness-test
PASSED: speech-to-text-readiness-test
RUNNING: text-to-speech-readiness-test
PASSED: text-to-speech-readiness-test
```

*Held testlerini*yürütmeye alternatif olarak, *dış IP* adreslerini ve karşılık gelen bağlantı noktalarını `kubectl get all` komutundan toplayabilirsiniz. IP ve bağlantı noktasını kullanarak bir Web tarayıcısı açın ve `http://<external-ip>:<port>:/swagger/index.html` ' a giderek API Swagger sayfasını görüntüleyin.

## <a name="customize-helm-charts"></a>Held grafiklerini özelleştirme

Held grafikleri hiyerarşiktir. Hiyerarşik olması, grafik devralmasına izin verir. Ayrıca, devralınan kuralların daha belirgin bir şekilde geçersiz kılınması gibi ayarlar, devralma kavramıyla de aynıdır.

[!INCLUDE [Speech umbrella-helm-chart-config](includes/speech-umbrella-helm-chart-config.md)]

[!INCLUDE [Speech-to-Text Helm Chart Config](includes/speech-to-text-chart-config.md)]

[!INCLUDE [Text-to-Speech Helm Chart Config](includes/text-to-speech-chart-config.md)]

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
[ms-helm-hub]: https://hub.helm.sh/charts/microsoft
[ms-helm-hub-speech-chart]: https://hub.helm.sh/charts/microsoft/cognitive-services-speech-onpremise

<!-- LINKS - internal -->
[speech-container-host-computer]: speech-container-howto.md#the-host-computer
[installing-helm-apps-in-aks]: ../../aks/kubernetes-helm.md
[cog-svcs-containers]: ../cognitive-services-container-support.md
