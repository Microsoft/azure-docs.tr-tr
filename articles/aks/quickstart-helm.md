---
title: Held ile Azure Kubernetes hizmeti (AKS) üzerinde geliştirme
description: Uygulama kapsayıcılarını bir kümede paketlemek ve çalıştırmak için AKS ile Held 'yi kullanın ve Azure Container Registry.
services: container-service
author: zr-msft
ms.topic: article
ms.date: 03/15/2021
ms.author: zarhoads
ms.openlocfilehash: e293d0c58f265b25f3df0a218f84888467468f59
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767502"
---
# <a name="quickstart-develop-on-azure-kubernetes-service-aks-with-helm"></a>Hızlı başlangıç: Held ile Azure Kubernetes hizmeti (AKS) üzerinde geliştirme

[Helk][helm] , Kubernetes uygulamalarının yaşam döngüsünü yüklemenize ve yönetmenize yardımcı olan bir açık kaynaklı paketleme aracıdır. *Apt* ve *yum* gibi Linux paket yöneticilerine benzer şekilde, Helm, önceden yapılandırılmış Kubernetes kaynakları paketleri olan Kubernetes grafiklerini yönetir.

Bu hızlı başlangıçta, AKS üzerinde bir uygulamayı paketlemek ve çalıştırmak için Held kullanacaksınız. Held kullanarak var olan bir uygulamayı yükleme hakkında daha fazla bilgi için bkz. AKS nasıl yapılır Kılavuzu ['nda mevcut uygulamaları Held Ile yükleme][helm-existing] .

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği. Azure aboneliğiniz yoksa [ücretsiz hesap](https://azure.microsoft.com/free) oluşturabilirsiniz.
* [Yüklü Azure CLI](/cli/azure/install-azure-cli).
* [Held v3 yüklendi][helm-install].

## <a name="create-an-azure-container-registry"></a>Azure Container Registry oluşturma
Bilgisayarınızı AKS kümenizde Helm kullanarak çalıştırmak için kapsayıcı görüntülerinizi bir Azure Container Registry (ACR) olarak depolamanız gerekir. Azure 'da benzersiz bir kayıt defteri adı sağlayın ve 5-50 alfasayısal karakter içerir. *Temel* SKU, geliştirme amaçlı dağıtımlar için uygun maliyetli, depolama ve aktarım hızı açısından dengeli bir giriş noktasıdır.

Aşağıdaki örnek, *Basic* SKU 'Su Ile *Myresourcegroup* Içinde *myhelmacr* adlı bir ACR oluşturmak için [az ACR Create][az-acr-create] komutunu kullanır.

```azurecli
az group create --name MyResourceGroup --location eastus
az acr create --resource-group MyResourceGroup --name MyHelmACR --sku Basic
```

Çıktı aşağıdaki örneğe benzer olacaktır. Daha sonraki bir adımda kullanacağınız için ACR 'niz için *Loginserver* değerini göz önünde bulabilirsiniz. Aşağıdaki örnekte, *Myhelmacr.azurecr.io* *Myhelmacr* için *loginserver* ' dır.

```console
{
  "adminUserEnabled": false,
  "creationDate": "2019-06-11T13:35:17.998425+00:00",
  "id": "/subscriptions/<ID>/resourceGroups/MyResourceGroup/providers/Microsoft.ContainerRegistry/registries/MyHelmACR",
  "location": "eastus",
  "loginServer": "myhelmacr.azurecr.io",
  "name": "MyHelmACR",
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

## <a name="create-an-aks-cluster"></a>AKS kümesi oluşturma

Yeni AKS kümenizin, kapsayıcı görüntülerini çekmek ve çalıştırmak için ACR 'nize erişmesi gerekir. Şunları yapmak için aşağıdaki komutu kullanın:
* *Myaks* adlı bir aks kümesi oluşturun ve *Myhelmacr* öğesini ekleyin.
* *Myaks* kümesine *myhelmacr* ACR 'nize erişim izni verin.


```azurecli
az aks create -g MyResourceGroup -n MyAKS --location eastus  --attach-acr MyHelmACR --generate-ssh-keys
```

## <a name="connect-to-your-aks-cluster"></a>AKS kümenize bağlanma

Bir Kubernetes kümesini yerel olarak bağlamak için Kubernetes komut satırı istemcisini ( [kubectl][kubectl]) kullanın. `kubectl` Azure Cloud Shell kullanıyorsanız, zaten yüklüdür. 

1. `kubectl`Komutunu kullanarak yerel olarak yükler `az aks install-cli` :

    ```azurecli
    az aks install-cli
    ```

2. `kubectl`Komutunu kullanarak Kubernetes kümenize bağlanacak şekilde yapılandırın `az aks get-credentials` . Aşağıdaki komut örneği, *Myresourcegroup* Içinde *myaks* adlı aks kümesi için kimlik bilgilerini alır:  

    ```azurecli
    az aks get-credentials --resource-group MyResourceGroup --name MyAKS
    ```

## <a name="download-the-sample-application"></a>Örnek uygulamayı indirin:

Bu hızlı başlangıç [, Azure dev Spaces örnek deposundan bir örnek Node.js uygulaması][example-nodejs]kullanır. Uygulamayı GitHub 'dan kopyalayın ve `dev-spaces/samples/nodejs/getting-started/webfrontend` dizine gidin.

```console
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/nodejs/getting-started/webfrontend
```

## <a name="create-a-dockerfile"></a>Dockerfile oluşturma

Aşağıdaki komutları kullanarak yeni bir *Dockerfile* dosyası oluşturun:

```dockerfile
FROM node:latest

WORKDIR /webfrontend

COPY package.json ./

RUN npm install

COPY . .

EXPOSE 80
CMD ["node","server.js"]
```

## <a name="build-and-push-the-sample-application-to-the-acr"></a>Örnek uygulamayı derleyin ve ACR 'ye gönderin

Yukarıdaki Dockerfile 'ı kullanarak, kayıt defterine görüntü derlemek ve göndermek için [az ACR Build][az-acr-build] komutunu çalıştırın. `.`Komutun sonunda, Dockerfile konumunu (Bu durumda, geçerli dizin) ayarlar.

```azurecli
az acr build --image webfrontend:v1 \
  --registry MyHelmACR \
  --file Dockerfile .
```

## <a name="create-your-helm-chart"></a>Held grafiğinizi oluşturma

Komutunu kullanarak Held grafiğinizi oluşturun `helm create` .

```console
helm create webfrontend
```

*Web ön uç/değerlerini güncelleştir. YAML*:
* *Myhelmacr.azurecr.io* gibi önceki bir adımda not ettiğiniz kayıt defterinizin loginserver ' ı değiştirin.
* Değiştir `image.repository``<loginServer>/webfrontend`
* Değiştir `service.type``LoadBalancer`

Örnek:

```yml
# Default values for webfrontend.
# This is a YAML-formatted file.
# Declare variables to be passed into your templates.

replicaCount: 1

image:
  repository: myhelmacr.azurecr.io/webfrontend
  pullPolicy: IfNotPresent
...
service:
  type: LoadBalancer
  port: 80
...
```

`appVersion` `v1` *Web ön ucu/grafik. YAML*'de olarak güncelleştirin. Örneğin:

```yml
apiVersion: v2
name: webfrontend
...
# This is the version number of the application being deployed. This version number should be
# incremented each time you make changes to the application.
appVersion: v1
```

## <a name="run-your-helm-chart"></a>Held grafiğinizi çalıştırma

Komutunu kullanarak Held grafiğinizi kullanarak uygulamanızı yüklersiniz `helm install` .

```console
helm install webfrontend webfrontend/
```

Hizmetin genel bir IP adresi döndürmesi birkaç dakika sürer. `kubectl get service`Komutunu bağımsız değişkeniyle kullanarak ilerlemeyi izleyin `--watch` .

```console
$ kubectl get service --watch

NAME                TYPE          CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
webfrontend         LoadBalancer  10.0.141.72   <pending>     80:32150/TCP   2m
...
webfrontend         LoadBalancer  10.0.141.72   <EXTERNAL-IP> 80:32150/TCP   7m
```

Örnek uygulamayı görmek için kullanarak bir tarayıcıda uygulamanızın yük dengeleyicisine gidin `<EXTERNAL-IP>` .

## <a name="delete-the-cluster"></a>Küme silme

Kaynak grubunu, AKS kümesini, kapsayıcı kayıt defterini, ACR 'de depolanan kapsayıcı görüntülerini ve tüm ilgili kaynakları kaldırmak için [az Group Delete][az-group-delete] komutunu kullanın.

```azurecli-interactive
az group delete --name MyResourceGroup --yes --no-wait
```

> [!NOTE]
> Kümeyi sildiğinizde, AKS kümesi tarafından kullanılan Azure Active Directory hizmet sorumlusu kaldırılmaz. Hizmet sorumlusunu kaldırma adımları için bkz. [AKS hizmet sorumlusuyla ilgili önemli noktalar ve silme][sp-delete].
> 
> Yönetilen bir kimlik kullandıysanız, kimlik platform tarafından yönetilir ve kaldırma gerektirmez.

## <a name="next-steps"></a>Sonraki adımlar

Held kullanma hakkında daha fazla bilgi için HELI belgelerine bakın.

> [!div class="nextstepaction"]
> [Helm belgeleri][helm-documentation]

[az-acr-create]: /cli/azure/acr#az_acr_create
[az-acr-build]: /cli/azure/acr#az_acr_build
[az-group-delete]: /cli/azure/group#az_group_delete
[az aks get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az aks install-cli]: /cli/azure/aks#az_aks_install_cli
[example-nodejs]: https://github.com/Azure/dev-spaces/tree/master/samples/nodejs/getting-started/webfrontend
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[helm]: https://helm.sh/
[helm-documentation]: https://helm.sh/docs/
[helm-existing]: kubernetes-helm.md
[helm-install]: https://helm.sh/docs/intro/install/
[sp-delete]: kubernetes-service-principal.md#additional-considerations
