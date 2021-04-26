---
title: Held grafiklerini depola
description: Azure Container Registry içindeki depoları kullanarak Kubernetes uygulamalarınız için hele grafiklerini depolamayı öğrenin
ms.topic: article
ms.date: 04/15/2021
ms.openlocfilehash: c7dcdf222e9628daedb7e1c3617efb0b9c7af185
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772388"
---
# <a name="push-and-pull-helm-charts-to-an-azure-container-registry"></a>Azure Container Registry 'de Held grafikleri gönderme ve çekme

Kubernetes uygulamalarını hızlıca yönetmek ve dağıtmak için, [Açık kaynaklı HELI paket yöneticisini][helm]kullanabilirsiniz. Held ile uygulama paketleri, bir [Held grafik deposunda](https://helm.sh/docs/topics/chart_repository/)toplanan ve depolanan [grafikler](https://helm.sh/docs/topics/charts/)olarak tanımlanır.

Bu makalede, Heln 3 komutlarını kullanarak bir Azure Container Registry 'de hele grafik depolarının nasıl barındırılacağını gösterilmektedir. Birçok senaryoda, geliştirdiğiniz uygulamalar için kendi grafiklerinizi derleyip karşıya yüklersiniz. Kendi HELI grafiklerinizi oluşturma hakkında daha fazla bilgi için bkz. [grafik şablonu Geliştirici Kılavuzu][develop-helm-charts]. Ayrıca, başka bir Held deposundan var olan bir Helu grafiğini de saklayabilirsiniz.

## <a name="helm-3-or-helm-2"></a>Helb 3 veya Held 2?

Held grafiklerini depolamak, yönetmek ve yüklemek için bir hele istemcisi ve Held CLı kullanırsınız. Held istemcisinin ana sürümleri, helm3 ve Held 2 ' dir. Sürüm farklılıkları hakkındaki ayrıntılar için bkz. [Sürüm SSS](https://helm.sh/docs/faq/). 

Hele grafiklerini Azure Container Registry barındırmak için helm3 kullanılmalıdır. Helb 3 ile şunları yapabilirsiniz:

* Bir Azure Container Registry 'de bir veya daha fazla Held deposu oluşturabilir
* Helm3 grafiklerini bir kayıt defterinde [OCI yapıtları](container-registry-image-formats.md#oci-artifacts)olarak depolayın. Azure Container Registry, Held grafikleri dahil [OCI yapıtları](container-registry-oci-artifacts.md)için GA desteği sağlar.
* Komutunu kullanarak kayıt defterinizde kimlik doğrulaması yapın `helm registry login` .
* `helm chart`Bir kayıt defterinde Held grafiklerini göndermek, çekmek ve yönetmek Için HELı CLI içindeki komutları kullanın
* `helm install`Yerel bir depo önbelleğinden bir Kubernetes kümesine grafik yüklemek için kullanın.
> [!NOTE]
> Helm 3 itibariyle, Helm 2 istemcisiyle kullanım için [az ACR Helm][az-acr-helm] komutları kullanım dışı bırakılıyor. Komut kaldırma işleminin ardından en az 3 ay ' bildirim sunulacaktır. Daha önce helk 2 grafikleri dağıttıysanız, bkz. [Held v2 'yi v3 'e geçirme](https://helm.sh/docs/topics/v2_v3_migration/).

## <a name="prerequisites"></a>Önkoşullar

Bu makaledeki senaryo için aşağıdaki kaynaklar gereklidir:

- Azure aboneliğinizdeki **bir Azure Kapsayıcı kayıt defteri** . Gerekirse, [Azure Portal](container-registry-get-started-portal.md) veya [Azure CLI](container-registry-get-started-azure-cli.md)kullanarak bir kayıt defteri oluşturun.
- **Hele Client Version 3.1.0 veya üzeri** - `helm version` geçerli sürümünüzü bulmak için çalıştırın. Held 'yi yükleme ve yükseltme hakkında daha fazla bilgi için bkz. [Held yükleme][helm-install].
- Helk grafiğini yükleyeceğiniz **bir Kubernetes kümesi** . Gerekirse, bir [Azure Kubernetes hizmet kümesi][aks-quickstart]oluşturun. 
- Sürümü bulmak için **Azure CLI sürüm 2.0.71 veya üzeri** -çalıştırın `az --version` . Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure CLI yükleme][azure-cli-install].

## <a name="enable-oci-support"></a>OCı desteğini etkinleştir

`helm version`Held 3 ' ü yüklediğinizi doğrulamak için komutunu kullanın:

```console
helm version
```

Hele 3 istemcisinde OCı desteğini etkinleştirmek için aşağıdaki ortam değişkenini ayarlayın. Şu anda bu destek deneysel bir değer. 

```console
export HELM_EXPERIMENTAL_OCI=1
```

## <a name="create-a-sample-chart"></a>Örnek grafik oluşturma

Aşağıdaki komutları kullanarak bir test grafiği oluşturun:

```console
mkdir helmtest

cd helmtest
helm create hello-world
```

Temel bir örnek olarak, dizini klasörü olarak değiştirin `templates` ve ilk olarak içeriğini silin:

```console
cd hello-world/templates
rm -rf *
```

Klasöründe, `templates` aşağıdaki komutu çalıştırarak adlı bir dosya oluşturun `configmap.yaml` :

```console
cat <<EOF > configmap.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: hello-world-configmap
data:
  myvalue: "Hello World"
EOF
```

Bu örneği oluşturma ve çalıştırma hakkında daha fazla bilgi için, bkz. HELI docs 'ta [çalışmaya](https://helm.sh/docs/chart_template_guide/getting_started/) başlama.

## <a name="save-chart-to-local-registry-cache"></a>Grafiği yerel kayıt defteri önbelleğine Kaydet

Dizini `hello-world` alt dizine değiştirin. Ardından, `helm chart save` grafiğin bir kopyasını yerel olarak kaydetmek ve ayrıca, kayıt defterinin tam adı (tümü küçük) ve hedef depo ve etiket ile bir diğer ad oluşturmak için öğesini çalıştırın. 

Aşağıdaki örnekte, kayıt defteri adı *mycontainerregistry*, hedef depo ise *Merhaba-Dünya* ve hedef grafik etiketi *v1*, ancak ortamınız için de değer yerine kullanılır:

```console
cd ..
helm chart save . hello-world:v1
helm chart save . mycontainerregistry.azurecr.io/helm/hello-world:v1
```

`helm chart list`Grafikleri yerel kayıt defteri önbelleğine kaydettiğinizden emin olmak için ' i çalıştırın. Çıkış şuna benzer olacaktır:

```console
REF                                                      NAME            VERSION DIGEST  SIZE            CREATED
hello-world:v1                                           hello-world       0.1.0   5899db0 3.2 KiB        2 minutes 
mycontainerregistry.azurecr.io/helm/hello-world:v1       hello-world       0.1.0   5899db0 3.2 KiB        2 minutes
```

## <a name="authenticate-with-the-registry"></a>Kayıt defteriyle kimlik doğrulama

`helm registry login`Senaryolarınız için uygun kimlik bilgilerini kullanarak [kayıt defteriyle kimlik doğrulaması](container-registry-authentication.md) yapmak için HELB 3 CLI içinde komutunu çalıştırın.

Örneğin, kayıt defterine [çekme ve itme izinleri](container-registry-auth-service-principal.md#create-a-service-principal) (acrpush rolü) ile bir Azure Active Directory hizmet sorumlusu oluşturun. Ardından hizmet sorumlusu kimlik bilgilerini sağlayın `helm registry login` . Aşağıdaki örnek, bir ortam değişkeni kullanarak parolayı sağlar:

```console
echo $spPassword | helm registry login mycontainerregistry.azurecr.io \
  --username <service-principal-id> \
  --password-stdin
```

## <a name="push-chart-to-registry"></a>Grafiği kayıt defterine gönder

`helm chart push`Grafiği tam nitelikli hedef depoya göndermek Için helm3 CLI içinde komutunu çalıştırın:

```console
helm chart push mycontainerregistry.azurecr.io/helm/hello-world:v1
```

Başarılı bir gönderim sonrasında, çıkış şuna benzerdir:

```output
The push refers to repository [mycontainerregistry.azurecr.io/helm/hello-world]
ref:     mycontainerregistry.azurecr.io/helm/hello-world:v1
digest:  5899db028dcf96aeaabdadfa5899db025899db025899db025899db025899db02
size:    3.2 KiB
name:    hello-world
version: 0.1.0
```

## <a name="list-charts-in-the-repository"></a>Depodaki grafikleri listeleme

Azure Container Registry 'de depolanan görüntülerde olduğu gibi, grafiklerinizi barındıran depoları, grafik etiketlerini ve bildirimleri göstermek için [az ACR Repository][az-acr-repository] komutlarını kullanabilirsiniz. 

Örneğin, önceki adımda oluşturduğunuz deponun özelliklerini görmek için [az ACR Repository Show][az-acr-repository-show] ' ı çalıştırın:

```azurecli
az acr repository show \
  --name mycontainerregistry \
  --repository helm/hello-world
```

Çıkış şuna benzer olacaktır:

```output
{
  "changeableAttributes": {
    "deleteEnabled": true,
    "listEnabled": true,
    "readEnabled": true,
    "writeEnabled": true
  },
  "createdTime": "2020-03-20T18:11:37.6701689Z",
  "imageName": "helm/hello-world",
  "lastUpdateTime": "2020-03-20T18:11:37.7637082Z",
  "manifestCount": 1,
  "registry": "mycontainerregistry.azurecr.io",
  "tagCount": 1
}
```

Depoda depolanan grafiğin ayrıntılarını görmek için [az ACR Repository Show-bildirimleri][az-acr-repository-show-manifests] komutunu çalıştırın. Örnek:

```azurecli
az acr repository show-manifests \
  --name mycontainerregistry \
  --repository helm/hello-world --detail
```

Bu örnekte kısaltılmış çıkış, ' a şunu gösterir `configMediaType` `application/vnd.cncf.helm.config.v1+json` :

```output
[
  {
    [...]
    "configMediaType": "application/vnd.cncf.helm.config.v1+json",
    "createdTime": "2020-03-20T18:11:37.7167893Z",
    "digest": "sha256:0c03b71c225c3ddff53660258ea16ca7412b53b1f6811bf769d8c85a1f0663ee",
    "imageSize": 3301,
    "lastUpdateTime": "2020-03-20T18:11:37.7167893Z",
    "mediaType": "application/vnd.oci.image.manifest.v1+json",
    "tags": [
      "v1"
    ]
```

## <a name="pull-chart-to-local-cache"></a>Grafiği yerel önbelleğe çekme

Kubernetes 'e bir helk grafiği yüklemek için, grafiğin yerel önbellekte olması gerekir. Bu örnekte, önce `helm chart remove` aşağıdaki adlı var olan yerel grafiği kaldırmak için öğesini çalıştırın `mycontainerregistry.azurecr.io/helm/hello-world:v1` :

```console
helm chart remove mycontainerregistry.azurecr.io/helm/hello-world:v1
```

`helm chart pull`Grafiği Azure Container Registry 'den yerel önbelleğinize indirmek için çalıştırın:

```console
helm chart pull mycontainerregistry.azurecr.io/helm/hello-world:v1
```

## <a name="export-helm-chart"></a>Held grafiğini dışarı aktar

Grafikle daha fazla çalışmak için, kullanarak yerel bir dizine dışarı aktarın `helm chart export` . Örneğin, dizine çekilecek grafiği dışarı aktarın `install` :

```console
helm chart export mycontainerregistry.azurecr.io/helm/hello-world:v1 \
  --destination ./install
```

Depoda, içe aktarılmış grafik bilgilerini görüntülemek için, `helm show chart` grafiği verdiğiniz dizinde komutunu çalıştırın.

```console
cd install
helm show chart hello-world
```

HELI, aşağıdaki örnek çıktıda gösterildiği gibi, grafiğinizin en son sürümü hakkında ayrıntılı bilgiler döndürür:

```output
apiVersion: v2
appVersion: 1.16.0
description: A Helm chart for Kubernetes
name: hello-world
type: application
version: 0.1.0    
```

## <a name="install-helm-chart"></a>Held grafiğini yükler

`helm install`Yerel önbelleğe ve dışarıya verdiğiniz Held grafiğini yüklemek için öğesini çalıştırın. *Myhelmtest* gibi bir yayın adı belirtin veya `--generate-name` parametreyi geçirin. Örnek:

```console
helm install myhelmtest ./hello-world
```

Başarılı grafik yüklemesinden sonra oluşan çıkış şuna benzerdir:

```console
NAME: myhelmtest
LAST DEPLOYED: Fri Mar 20 14:14:42 2020
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
```

Yüklemeyi doğrulamak için `helm get manifest` komutunu çalıştırın. 

```console
helm get manifest myhelmtest
```

Komut, şablon dosyanızdaki YAML verilerini döndürür `configmap.yaml` .

`helm uninstall`Kümenizdeki grafik sürümünü kaldırmak için öğesini çalıştırın:

```console
helm uninstall myhelmtest
```

## <a name="delete-chart-from-the-registry"></a>Kayıt defterinden grafik silme

Bir grafiği kapsayıcı kayıt defterinden silmek için [az ACR Repository Delete][az-acr-repository-delete] komutunu kullanın. Aşağıdaki komutu çalıştırın ve sorulduğunda işlemi onaylayın:

```azurecli
az acr repository delete --name mycontainerregistry --image helm/hello-world:v1
```

## <a name="next-steps"></a>Sonraki adımlar

* HELI grafikleri oluşturma ve dağıtma hakkında daha fazla bilgi için bkz. [HELI grafikleri geliştirme][develop-helm-charts].
* [Azure Kubernetes Service 'te (AKS)](../aks/kubernetes-helm.md)Held ile uygulama yükleme hakkında daha fazla bilgi edinin.
* Held grafikleri kapsayıcı oluşturma sürecinin bir parçası olarak kullanılabilir. Daha fazla bilgi için bkz. [Azure Container Registry görevleri kullanma][acr-tasks].

<!-- LINKS - external -->
[helm]: https://helm.sh/
[helm-install]: https://helm.sh/docs/intro/install/
[develop-helm-charts]: https://helm.sh/docs/chart_template_guide/
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-quickstart]: ../aks/kubernetes-walkthrough.md
[acr-bestpractices]: container-registry-best-practices.md
[az-configure]: /cli/azure/reference-index#az_configure
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-acr-helm]: /cli/azure/acr/helm
[az-acr-repository]: /cli/azure/acr/repository
[az-acr-repository-show]: /cli/azure/acr/repository#az_acr_repository_show
[az-acr-repository-delete]: /cli/azure/acr/repository#az_acr_repository_delete
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az_acr_repository_show_tags
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az_acr_repository_show_manifests
[acr-tasks]: container-registry-tasks-overview.md
