---
title: Azure Kubernetes hizmeti (AKS) üzerinde taslak ile geliştirme
description: AKS ve Azure Container Registry taslağı kullanın
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 06/20/2019
ms.author: zarhoads
ms.openlocfilehash: bd099b9d76e17eda36be1650ef5081e5aaa7e53a
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/30/2019
ms.locfileid: "67303537"
---
# <a name="quickstart-develop-on-azure-kubernetes-service-aks-with-draft"></a>Hızlı Başlangıç: Azure Kubernetes hizmeti (AKS) üzerinde taslak ile geliştirme

Taslak, bir Kubernetes kümesinde paket oluşturma ve uygulama kapsayıcılarının çalıştırılmasına yardımcı olan açık kaynaklı bir araçtır. Taslak sayesinde, değişiklikleri sürüm denetimine kaydetmeye gerek kalmadan, kod değişiklikleri gerçekleştiğinden, bir uygulamayı Kubernetes 'e hızlıca yeniden dağıtabilirsiniz. Taslak hakkında daha fazla bilgi için [GitHub 'Daki taslak belgelerine][draft-documentation]bakın.

Bu makalede taslak paketin nasıl kullanılacağı ve AKS üzerinde bir uygulamanın nasıl çalıştırılacağı gösterilmektedir.


## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği. Azure aboneliğiniz yoksa [ücretsiz hesap](https://azure.microsoft.com/free) oluşturabilirsiniz.
* [Yüklü Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).
* Docker yüklendi ve yapılandırıldı. Docker, bir [Mac][docker-for-mac], [Windows][docker-for-windows]veya [Linux][docker-for-linux] sisteminde Docker 'ı yapılandıran paketler sağlar.
* [Held yüklendi](https://github.com/helm/helm/blob/master/docs/install.md).
* [Taslak yüklendi][draft-documentation].

## <a name="create-an-azure-kubernetes-service-cluster"></a>Azure Kubernetes hizmet kümesi oluşturma

AKS kümesi oluşturma. Aşağıdaki komutlar MyResourceGroup adlı bir kaynak grubu ve MyAKS adlı bir AKS kümesi oluşturur.

```azurecli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --node-vm-size Standard_DS2_v2 --node-count 1 --generate-ssh-keys
```

## <a name="create-an-azure-container-registry"></a>Azure Container Registry oluşturma
Uygulamanızı AKS kümenizde çalıştırmak üzere taslağı kullanmak için, kapsayıcı görüntülerinizi depolamak için bir Azure Container Registry gerekir. Aşağıdaki örnek, *temel* SKU Ile *myresourcegroup* kaynak grubunda *mydraftacr* adlı bir ACR oluşturmak için [az ACR Create][az-acr-create] komutunu kullanır. Kendi benzersiz kayıt defteriniz adını sağlamalısınız. Kaynak defteri adı Azure’da benzersiz olmalı ve 5-50 arası alfasayısal karakter içermelidir. *Temel* SKU, geliştirme amaçlı dağıtımlar için uygun maliyetli, depolama ve aktarım hızı açısından dengeli bir giriş noktasıdır.

```azurecli
az acr create --resource-group MyResourceGroup --name MyDraftACR --sku Basic
```

Çıktı aşağıdaki örneğe benzerdir. Daha sonraki bir adımda kullanılabileceğinizden, ACR 'niz için *Loginserver* değerini bir yere göz önünde koyun. Aşağıdaki örnekte, *Mydraftacr.azurecr.io* *Mydraftacr*için *loginserver* ' dır.

```console
{
  "adminUserEnabled": false,
  "creationDate": "2019-06-11T13:35:17.998425+00:00",
  "id": "/subscriptions/<ID>/resourceGroups/MyResourceGroup/providers/Microsoft.ContainerRegistry/registries/MyDraftACR",
  "location": "eastus",
  "loginServer": "mydraftacr.azurecr.io",
  "name": "MyDraftACR",
  "networkRuleSet": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "MyResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```


Taslak için ACR örneğini kullanmak üzere öncelikle oturum açmalısınız. Oturum açmak için [az ACR Login][az-acr-login] komutunu kullanın. Aşağıdaki örnek, *Mydraftacr*adlı bir ACR 'de oturum açacaktır.

```azurecli
az acr login --name MyDraftACR
```

Komut tamamlandığında bir *Oturum Başarıyla Açıldı* iletisi döndürür.

## <a name="create-trust-between-aks-cluster-and-acr"></a>AKS kümesi ve ACR arasında güven oluşturma

AKS kümenizin Ayrıca, kapsayıcı görüntülerini çekmek ve çalıştırmak için ACR 'nize erişmesi gerekir. Güven kurarak AKS 'ten ACR 'ye erişime izin veriyorsunuz. AKS kümesi ve ACR kayıt defteri arasında güven oluşturmak için, AKS kümesi tarafından ACR kayıt defterine erişmek üzere kullanılan Azure Active Directory hizmet sorumlusu için izin verin. Aşağıdaki komutlar myresourcegroup içindeki *Myaks* kümesinin hizmet sorumlusu Için *Myresourcegroup*Içindeki *mydraftacr* ACR *öğesine izinler* verir.

```azurecli
# Get the service principal ID of your AKS cluster
AKS_SP_ID=$(az aks show --resource-group MyResourceGroup --name MyAKS --query "servicePrincipalProfile.clientId" -o tsv)

# Get the resource ID of your ACR instance
ACR_RESOURCE_ID=$(az acr show --resource-group MyResourceGroup --name MyDraftACR --query "id" -o tsv)

# Create a role assignment for your AKS cluster to access the ACR instance
az role assignment create --assignee $AKS_SP_ID --scope $ACR_RESOURCE_ID --role contributor
```

## <a name="connect-to-your-aks-cluster"></a>AKS kümenize bağlanma

Yerel bilgisayarınızdan Kubernetes kümesine bağlanmak için Kubernetes komut satırı istemcisi olan [kubectl][kubectl]'yi kullanırsınız.

Azure Cloud Shell'i kullanıyorsanız `kubectl` zaten yüklüdür. [az aks install-cli][] komutunu kullanarak da yerel ortama yükleyebilirsiniz:

```azurecli
az aks install-cli
```

Kubernetes kümenize bağlanacak şekilde yapılandırmak `kubectl` için [az aks Get-Credentials][] komutunu kullanın. Aşağıdaki örnek, *Myresourcegroup*Içinde *myaks* adlı aks kümesi için kimlik bilgilerini alır:

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
```

## <a name="create-a-service-account-for-helm"></a>Held için bir hizmet hesabı oluşturma

Helm 'yi RBAC özellikli bir AKS kümesinde dağıtabilmeniz için, Tiller hizmeti için bir hizmet hesabı ve rol bağlaması olması gerekir. RBAC etkin bir kümede Held/Tiller güvenliğini sağlama hakkında daha fazla bilgi için bkz. [Tiller, namespaces ve RBAC][tiller-rbac]. AKS kümeniz RBAC etkinleştirilmemişse, bu adımı atlayın.

Aşağıdaki YAML 'de `helm-rbac.yaml` adlı bir dosya oluşturun ve kopyalayın:

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: tiller
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: tiller
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
  - kind: ServiceAccount
    name: tiller
    namespace: kube-system
```

`kubectl apply` Komutuyla hizmet hesabı ve rol bağlamayı oluşturun:

```console
kubectl apply -f helm-rbac.yaml
```

## <a name="configure-helm"></a>Held 'yi yapılandırma
Bir AKS kümesine temel bir Tiller dağıtmak için [helk init][helm-init] komutunu kullanın. Kümeniz RBAC etkinleştirilmemişse, `--service-account` bağımsız değişkeni ve değeri kaldırın.

```console
helm init --service-account tiller --node-selectors "beta.kubernetes.io/os"="linux"
```

## <a name="configure-draft"></a>Taslağı yapılandırma

Yerel makinenizde taslak yapılandırmadıysanız şunu çalıştırın `draft init`:

```console
$ draft init
Installing default plugins...
Installation of default plugins complete
Installing default pack repositories...
...
Happy Sailing!
```

Ayrıca, taslağı, ACR 'nizin *Loginserver* 'ı kullanacak şekilde yapılandırmanız gerekir. Aşağıdaki komut, kayıt `draft config set` defteri olarak `mydraftacr.azurecr.io` kullanmak için kullanır.

```console
draft config set registry mydraftacr.azurecr.io
```

Taslağını ACR 'nizi kullanacak şekilde yapılandırdınız ve taslak kapsayıcı görüntülerini ACR 'nize gönderebilir. Taslak, uygulamanızı AKS kümenizde çalıştırdığında, ACR kayıt defterine göndermek veya buradan çekmek için parola veya gizli dizi gerekmez. AKS kümeniz ve ACR 'niz arasında bir güven oluşturulduğundan, Azure Active Directory kullanarak kimlik doğrulaması Azure Resource Manager düzeyinde gerçekleşir.

## <a name="download-the-sample-application"></a>Örnek uygulamayı indirin:

Bu hızlı başlangıçta [taslak GitHub deposundan örnek bir Java uygulaması][example-java]kullanılmaktadır. Uygulamayı GitHub 'dan kopyalayın ve `draft/examples/example-java/` dizine gidin.

```console
git clone https://github.com/Azure/draft
cd draft/examples/example-java/
```

## <a name="run-the-sample-application-with-draft"></a>Örnek uygulamayı taslak ile çalıştırma

Uygulamayı hazırlamak için komutunu kullanın. `draft create`

```console
draft create
```

Bu komut, bir Kubernetes kümesinde uygulamayı çalıştırmak için kullanılan yapıtları oluşturur. Bu öğeler, taslak yapılandırma dosyası olan bir Dockerfile, Hela grafiği ve bir *taslak. TOML* dosyası içerir.

```console
$ draft create

--> Draft detected Java (92.205567%)
--> Ready to sail
```

Örnek uygulamayı aks kümenizde çalıştırmak için `draft up` komutunu kullanın.

```console
draft up
```

Bu komut Dockerfile 'ı bir kapsayıcı görüntüsü oluşturmak, görüntüyü ACR 'nize iletmeleri ve AKS 'te uygulamayı başlatmak için Helm grafiğini yükler. Bu komutu ilk kez çalıştırdığınızda kapsayıcı görüntüsünü alıp çekerek biraz zaman alabilir. Temel katmanlar önbelleğe alındıktan sonra, uygulamanın dağıtılması için geçen süre önemli ölçüde azalır.

```
$ draft up

Draft Up Started: 'example-java': 01CMZAR1F4T1TJZ8SWJQ70HCNH
example-java: Building Docker Image: SUCCESS ⚓  (73.0720s)
example-java: Pushing Docker Image: SUCCESS ⚓  (19.5727s)
example-java: Releasing Application: SUCCESS ⚓  (4.6979s)
Inspect the logs with `draft logs 01CMZAR1F4T1TJZ8SWJQ70HCNH`
```

## <a name="connect-to-the-running-sample-application-from-your-local-machine"></a>Yerel makinenizden çalışan örnek uygulamaya bağlanma

Uygulamayı test etmek için `draft connect` komutunu kullanın.

```console
draft connect
```

Bu komut, Kubernetes Pod ile güvenli bir bağlantı proxy 'si sağlar. Bu tamamlandığında, uygulamaya, belirtilen URL üzerinden erişilebilir.

```console
$ draft connect

Connect to java:4567 on localhost:49804
[java]: SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
[java]: SLF4J: Defaulting to no-operation (NOP) logger implementation
[java]: SLF4J: See https://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
[java]: == Spark has ignited ...
[java]: >> Listening on 0.0.0.0:4567
```

Örnek uygulamayı görmek için `localhost` URL 'yi kullanarak bir tarayıcıda uygulamaya gidin. Yukarıdaki örnekte, URL olur `http://localhost:49804`. Kullanarak `Ctrl+c`bağlantıyı durdurun.

## <a name="access-the-application-on-the-internet"></a>Uygulamaya internet üzerinden erişin

Önceki adım, AKS kümenizdeki uygulama Pod 'sine bir ara sunucu bağlantısı oluşturdu. Uygulamanızı geliştirirken ve test etmeniz sırasında, uygulamayı Internet 'te kullanılabilir hale getirmek isteyebilirsiniz. Bir uygulamayı Internet 'te kullanıma sunmak için bir [yük dengeleyici][kubernetes-service-loadbalancer]türü Ile bir Kubernetes hizmeti oluşturabilirsiniz.

Bir `charts/example-java/values.yaml` *LoadBalancer* hizmeti oluşturmak için güncelleştirme. *Service. Type* değerini *ClusterIP* 'den *LoadBalancer*değerine değiştirin.

```yaml
...
service:
  name: java
  type: LoadBalancer
  externalPort: 80
  internalPort: 4567
...
```

Değişikliklerinizi kaydedin, dosyayı kapatın ve uygulamayı yeniden çalıştırmak için `draft up` çalıştırın.

```console
draft up
```

Hizmetin genel bir IP adresi döndürmesi birkaç dakika sürer. İlerlemeyi izlemek için, `kubectl get service` *Gözcü* parametresiyle komutunu kullanın:

```console
$ kubectl get service --watch

NAME                TYPE          CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
example-java-java   LoadBalancer  10.0.141.72   <pending>     80:32150/TCP   2m
...
example-java-java   LoadBalancer   10.0.141.72   52.175.224.118  80:32150/TCP   7m
```

Örnek uygulamayı görmek için *dış IP* 'yi kullanarak bir tarayıcıda uygulamanızın yük dengeleyicisine gidin. Yukarıdaki örnekte, IP olur `52.175.224.118`.

## <a name="iterate-on-the-application"></a>Uygulama üzerinde yineleme

Değişiklikleri yerel olarak yaparak ve yeniden çalıştırarak `draft up`uygulamanızı yineleyebilirsiniz.

[Src/Main/Java/HelloWorld/Hello. Java için 7. satırda][example-java-hello-l7] döndürülen iletiyi güncelleştirin

```java
    public static void main(String[] args) {
        get("/", (req, res) -> "Hello World, I'm Java in AKS!");
    }
```

Uygulamayı yeniden dağıtmak için komutunuçalıştırın:`draft up`

```console
$ draft up

Draft Up Started: 'example-java': 01CMZC9RF0TZT7XPWGFCJE15X4
example-java: Building Docker Image: SUCCESS ⚓  (25.0202s)
example-java: Pushing Docker Image: SUCCESS ⚓  (7.1457s)
example-java: Releasing Application: SUCCESS ⚓  (3.5773s)
Inspect the logs with `draft logs 01CMZC9RF0TZT7XPWGFCJE15X4`
```

Güncelleştirilmiş uygulamayı görmek için yük dengeleyicinizin IP adresine gidin ve değişikliklerinizin göründüğünü doğrulayın.

## <a name="delete-the-cluster"></a>Küme silme

Küme artık gerekli olmadığında, [az Group Delete][az-group-delete] komutunu kullanarak kaynak grubunu, aks kümesini, kapsayıcı kayıt defterini, orada depolanan kapsayıcı görüntülerini ve tüm ilgili kaynakları kaldırın.

```azurecli-interactive
az group delete --name MyResourceGroup --yes --no-wait
```

> [!NOTE]
> Kümeyi sildiğinizde, AKS kümesi tarafından kullanılan Azure Active Directory hizmet sorumlusu kaldırılmaz. Hizmet sorumlusunu kaldırma adımları için bkz. [aks hizmet sorumlusu konuları ve silme][sp-delete].

## <a name="next-steps"></a>Sonraki adımlar

Taslağı kullanma hakkında daha fazla bilgi için GitHub 'daki taslak belgelerine bakın.

> [!div class="nextstepaction"]
> [Taslak belge][draft-documentation]


[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-create]: /cli/azure/acr#az-acr-login
[az-group-delete]: /cli/azure/group#az-group-delete
[az aks get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az aks install-cli]: /cli/azure/aks#az-aks-install-cli
[kubernetes-ingress]: ./ingress-basic.md

[docker-for-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-for-mac]: https://docs.docker.com/docker-for-mac/
[docker-for-windows]: https://docs.docker.com/docker-for-windows/
[draft-documentation]: https://github.com/Azure/draft/tree/master/docs
[example-java]: https://github.com/Azure/draft/tree/master/examples/example-java
[example-java-hello-l7]: https://github.com/Azure/draft/blob/master/examples/example-java/src/main/java/helloworld/Hello.java#L7
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubernetes-service-loadbalancer]: https://kubernetes.io/docs/concepts/services-networking/service/#type-loadbalancer
[helm-init]: https://docs.helm.sh/helm/#helm-init
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[tiller-rbac]: https://docs.helm.sh/using_helm/#tiller-namespaces-and-rbac
