---
title: Azure’da Kubernetes öğreticisi - Uygulamayı dağıtma
description: Bu Azure Kubernetes Service (AKS) öğreticisinde, Azure Container Registry'de depolanan özel bir görüntüyü kullanarak kümenizde çok kapsayıcılı bir uygulama dağıtacaksınız.
services: container-service
ms.topic: tutorial
ms.date: 01/12/2021
ms.custom: mvc
ms.openlocfilehash: a0de097a545a831e39a671fe4cf5eadcd336ce24
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98250188"
---
# <a name="tutorial-run-applications-in-azure-kubernetes-service-aks"></a>Öğretici: Azure Kubernetes Hizmeti’nde (AKS) uygulamaları çalıştırma

Kubernetes, kapsayıcılı uygulamalar için dağıtılmış bir platform sunar. Kendi uygulamalarınızı ve hizmetlerinizi oluşturup bir Kubernetes kümesine dağıtırsınız, kullanılabilirlik ve bağlantı özellikleri de küme tarafından yönetilir. Yedi bölümün dördüncüsü olan bu öğreticide Kubernetes kümesine örnek bir uygulama dağıtılır. Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:

> [!div class="checklist"]
> * Kubernetes bildirim dosyasını güncelleştirme
> * Kubernetes'te uygulama çalıştırma
> * Uygulamayı test edin

Sonraki öğreticilerde, bu uygulama ölçeklendirilir ve güncelleştirilir.

Bu hızlı başlangıç, Kubernetes kavramlarının temel olarak bilindiğini varsayar. Daha fazla bilgi için bkz. [Azure Kubernetes hizmeti (AKS) Için Kubernetes temel kavramları][kubernetes-concepts].

## <a name="before-you-begin"></a>Başlamadan önce

Önceki öğreticilerde, bir uygulama bir kapsayıcı görüntüsüne paketlendi, görüntü Azure Container Registry’ye yüklendi ve bir Kubernetes kümesi oluşturuldu.

Bu öğreticiyi tamamlamak için önceden oluşturulmuş `azure-vote-all-in-one-redis.yaml` Kubernetes bildirim dosyasına ihtiyaç duyarsınız. Bu dosya, önceki öğreticide uygulama kaynak koduyla indirildi. Depoyu Klonladığınız ve dizinleri kopyalanmış depoya değiştirdiğinizi doğrulayın. Bu adımları tamamlamadıysanız ve takip etmek istiyorsanız, [öğretici 1 – kapsayıcı görüntüleri oluşturma][aks-tutorial-prepare-app]ile başlayın.

Bu öğreticide, Azure CLı sürüm 2.0.53 veya üstünü çalıştırıyor olmanız gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure CLI yükleme][azure-cli-install].

## <a name="update-the-manifest-file"></a>Bildirim dosyasını güncelleştirme

Bu öğreticilerde Azure Container Registry (ACR) örneği, örnek uygulamanın kapsayıcı görüntüsünü depolar. Uygulamayı dağıtmak için Kubernetes bildirim dosyası içindeki görüntü adını ACR oturum açma sunucusu adını içerecek şekilde güncelleştirmeniz gerekir.

ACR oturum açma sunucusunun adını almak için [az acr list][az-acr-list] komutunu aşağıda gösterilen şekilde kullanın:

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

İlk öğreticide kopyalanan git deposundaki örnek bildirim dosyasında oturum açma sunucusu adı olarak *microsoft* kullanılmıştır. Kopyalanmış *Azure-oylama-App-redsıs* dizininde olduğunuzdan emin olun, ardından bildirim dosyasını şöyle bir metin düzenleyicisiyle açın `vi` :

```console
vi azure-vote-all-in-one-redis.yaml
```

*microsoft* yerine ACR oturum açma sunucunuzun adını yazın. Görüntü adı, bildirim dosyasının 60. satırında bulunur. Aşağıdaki örnekte varsayılan görüntü adı gösterilir:

```yaml
containers:
- name: azure-vote-front
  image: mcr.microsoft.com/azuredocs/azure-vote-front:v1
```

Kendi ACR oturum açma sunucunuzun adını girerek bildirim dosyanızın aşağıdaki örnekte olduğu gibi görünmesini sağlayın:

```yaml
containers:
- name: azure-vote-front
  image: <acrName>.azurecr.io/azure-vote-front:v1
```

Dosyayı kaydedin ve kapatın. İçinde `vi` , kullanın `:wq` .

## <a name="deploy-the-application"></a>Uygulamayı dağıtma

Uygulamanızı dağıtmak için [kubectl apply][kubectl-apply] komutunu kullanın. Bu komut, bildirim dosyasını ayrıştırır ve tanımlanmış Kubernetes nesnelerini oluşturur. Aşağıdaki örnekte gösterildiği gibi örnek bildirim dosyasının adını belirtin:

```console
kubectl apply -f azure-vote-all-in-one-redis.yaml
```

Aşağıdaki örnek çıktıda, AKS kümesinde başarıyla oluşturulan kaynaklar gösterilmektedir:

```console
$ kubectl apply -f azure-vote-all-in-one-redis.yaml

deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-the-application"></a>Uygulamayı test edin

Uygulama çalıştığında, bir Kubernetes hizmeti, uygulamanın ön ucuna internet 'e koyar. Bu işlemin tamamlanması birkaç dakika sürebilir.

İlerlemeyi izlemek için, [kubectl Get Service][kubectl-get] komutunu bağımsız değişkeniyle birlikte kullanın `--watch` .

```console
kubectl get service azure-vote-front --watch
```

Başlangıçta *Azure-oyönme* hizmeti IÇIN *dış IP* , *Beklemede* olarak gösterilir:

```output
azure-vote-front   LoadBalancer   10.0.34.242   <pending>     80:30676/TCP   5s
```

*Dış IP* adresi *bekliyor* durumundan gerçek ortak IP adresi olarak değiştiğinde, `CTRL-C` izleme işlemini durdurmak için kullanın `kubectl` . Aşağıdaki örnek çıktıda, hizmete atanmış geçerli bir genel IP adresi gösterilmektedir:

```output
azure-vote-front   LoadBalancer   10.0.34.242   52.179.23.131   80:30676/TCP   67s
```

Uygulamayı eylemde görmek için, hizmetinizin dış IP adresine bir Web tarayıcısı açın:

:::image type="content" source="./media/container-service-kubernetes-tutorials/azure-vote.png" alt-text="Yerel Web tarayıcısında açılan bir AKS kümesinde çalıştırılan kapsayıcı görüntüsü Azure oylama uygulamasını gösteren ekran görüntüsü" lightbox="./media/container-service-kubernetes-tutorials/azure-vote.png":::

Uygulama yüklenmediyse, görüntü kayıt defterinizde bir yetkilendirme sorunundan kaynaklanıyor olabilir. Kapsayıcılarınızın durumunu görüntülemek için `kubectl get pods` komutunu kullanın. Kapsayıcı görüntüleri çekemediği takdirde bkz. [Azure Kubernetes hizmetinden Azure Container Registry kimlik doğrulaması](cluster-container-registry-integration.md).

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, AKS 'teki bir Kubernetes kümesine örnek bir Azure oy uygulaması dağıtıldı. Şunları öğrendiniz:

> [!div class="checklist"]
> * Kubernetes bildirim dosyalarını güncelleştirme
> * Kubernetes'te uygulama çalıştırma
> * Uygulamayı test edin

Kubernetes uygulamasını ve temel alınan Kubernetes altyapısını ölçeklendirme hakkında daha fazla bilgi için sonraki öğreticiye ilerleyin.

> [!div class="nextstepaction"]
> [Kubernetes uygulamasını ve altyapısını ölçeklendirme][aks-tutorial-scale]

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-scale]: ./tutorial-kubernetes-scale.md
[az-acr-list]: /cli/azure/acr
[azure-cli-install]: /cli/azure/install-azure-cli
[kubernetes-concepts]: concepts-clusters-workloads.md
[kubernetes-service]: concepts-network.md#services
