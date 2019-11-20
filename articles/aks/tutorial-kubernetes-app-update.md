---
title: Azure’da Kubernetes öğreticisi - Uygulamayı güncelleştirme
description: Bu Azure Kubernetes Service (AKS) öğreticisinde var olan bir AKS uygulama dağıtımını uygulama kodunun yeni sürümüyle güncelleştirmeyi öğreneceksiniz.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: mlearned
ms.custom: mvc
ms.openlocfilehash: b645fc9f67229d087a5d1655f733e2f3e50d4471
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67614388"
---
# <a name="tutorial-update-an-application-in-azure-kubernetes-service-aks"></a>Öğretici: Azure Kubernetes Service (AKS) uygulama güncelleştirme

Bir uygulama Kubernetes’te dağıtıldıktan sonra, yeni bir kapsayıcı görüntüsü veya görüntü sürümü belirtilerek güncelleştirilebilir. Bir güncelleştirme, aynı anda yalnızca bir kısmını dağıtım güncelleştirilebilmesi için hazırdır. Hazırlanan bu güncelleştirme, uygulamanın güncelleştirme sırasında çalışmaya devam etmesini sağlar. Ayrıca bir dağıtım hatası oluşursa, bir geri alma mekanizması sağlar.

Bu yedi parçalı öğreticinin altıncı bölümünde, örnek Azure Vote uygulaması güncelleştirilir. Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:

> [!div class="checklist"]
> * Ön uç uygulama kodunu güncelleştirme
> * Güncelleştirilmiş kapsayıcı görüntüsü oluşturma
> * Kapsayıcı görüntüsünü Azure Container Registry’ye gönderme
> * Güncelleştirilmiş kapsayıcı görüntüsünü dağıtma

## <a name="before-you-begin"></a>Başlamadan önce

Önceki öğreticilerde, bir uygulama bir kapsayıcı görüntüsüne paketlendi. Bu görüntü Azure Container Registry'ye yüklendi ve bir AKS kümesi oluşturulmakta. Uygulama ardından için AKS kümesi dağıtıldı.

Bu öğreticide ayrıca uygulama kaynak kodunu içeren bir uygulama deposu da kopyalandı ve önceden oluşturulmuş bir Docker Compose dosyası kullanıldı. Deponun bir kopyasını oluşturduktan ve dizinleri kopyalanmış dizine değiştirdiğinizi doğrulayın. Bu adımları tamamlamadıysanız ve takip etmek istiyorsanız başlayın [öğretici 1 – kapsayıcı görüntüleri oluşturma][aks-tutorial-prepare-app].

Bu öğretici, Azure CLI Sürüm 2.0.53 çalıştırdığınız gerektirir veya üzeri. Sürümü bulmak için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekiyorsa bkz. [Azure CLI'yı yükleme][azure-cli-install].

## <a name="update-an-application"></a>Uygulamayı güncelleştirme

Şimdi örnek uygulamada bir değişiklik yapalım ve AKS kümenize dağıtılmış olan sürümü güncelleştirelim. Kopyalanan olduğunuzdan emin olun *azure voting uygulamasını redis* dizin. Örnek uygulama kaynak kodunu ardından içinde bulunabilir *azure-vote* dizin. *config_file.cfg* dosyasını `vi` gibi bir düzenleyiciyle açın:

```console
vi azure-vote/azure-vote/config_file.cfg
```

Değerlerini değiştirmek *VOTE1VALUE* ve *VOTE2VALUE* renkleri gibi farklı değerler. Aşağıdaki örnek, güncelleştirilmiş değerleri gösterir:

```
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Blue'
VOTE2VALUE = 'Purple'
SHOWHOST = 'false'
```

Dosyayı kaydedin ve kapatın. İçinde `vi`, kullanın `:wq`.

## <a name="update-the-container-image"></a>Kapsayıcı görüntüsünü güncelleştirme

Ön uç görüntüsünü yeniden oluşturmak ve güncelleştirilmiş uygulamayı test etmek için kullanın [docker-compose][docker-compose]. `--build` bağımsız değişkeni, uygulama görüntüsünü yeniden oluşturmak üzere Docker Compose'a komut vermek için kullanılır:

```console
docker-compose up --build -d
```

## <a name="test-the-application-locally"></a>Uygulamayı yerel olarak test etme

Güncelleştirilmiş kapsayıcı görüntüsünde yaptığınız değişikliklerin göründüğünü doğrulamak için yerel web tarayıcısında `http://localhost:8080` adresine gidin.

![Azure’da Kubernetes kümesinin görüntüsü](media/container-service-kubernetes-tutorials/vote-app-updated.png)

Sağlanan güncelleştirilmiş değerleri *config_file.cfg* dosya çalışan uygulamanızda görüntülenir.

## <a name="tag-and-push-the-image"></a>Görüntüyü etiketleme ve gönderme

Güncelleştirilen görüntüyü doğru şekilde kullanmak için *azure-vote-front* görüntüsünü ACR kayıt defterinizin oturum açma sunucusunun adıyla etiketleyin. [az acr list](/cli/azure/acr) komutuyla oturum açma sunucusu adını alın:

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Görüntüyü etiketlemek için [docker tag][docker-tag]’i kullanın. Aşağıda gösterilen şekilde `<acrLoginServer>` yerine ACR oturum açma sunucunuzun adını veya genel kayıt defteri ana bilgisayar adını yazın ve görüntü sürümünü *:v2* olarak güncelleştirin:

```console
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v2
```

Artık [docker itme][docker-push] görüntüyü kayıt defterinize yüklemek için. `<acrLoginServer>` yerine ACR oturum açma sunucunuzun adını yazın.

> [!NOTE]
> ACR kayıt defterinize giden sorunlarla karşılaşıyorsanız, hala oturum açtığınızdan emin olun. Çalıştırma [az acr oturum açma][az-acr-login] komutunu kullanarak, oluşturduğunuz Azure Container Registry'nize adını [bir Azure Container Registry oluşturma](tutorial-kubernetes-prepare-acr.md#create-an-azure-container-registry) adım. Örneğin, `az acr login --name <azure container registry name>`.

```console
docker push <acrLoginServer>/azure-vote-front:v2
```

## <a name="deploy-the-updated-application"></a>Güncelleştirilmiş uygulamayı dağıtma

En uzun çalışma süresini sağlamak için uygulama podunun birden çok örneğini çalıştırılması gerekir. Ön uç örnekleri ile çalışan sayısını doğrulayın [kubectl pod'ları alma][kubectl-get] komutu:

```
$ kubectl get pods

NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-217588096-5w632    1/1       Running   0          10m
azure-vote-front-233282510-b5pkz   1/1       Running   0          10m
azure-vote-front-233282510-dhrtr   1/1       Running   0          10m
azure-vote-front-233282510-pqbfk   1/1       Running   0          10m
```

Ön uç birden çok podunuz yoksa, ölçeklendirme *azure-vote-front* aşağıdaki gibi dağıtım:

```console
kubectl scale --replicas=3 deployment/azure-vote-front
```

Uygulamayı güncelleştirmek için [kubectl set][kubectl-set] komutunu kullanın. `<acrLoginServer>` öğesini, kapsayıcı kayıt defterinizin oturum açma sunucusu veya ana bilgisayar adıyla güncelleştirin ve uygulama sürümünü *v2* olarak belirtin:

```console
kubectl set image deployment azure-vote-front azure-vote-front=<acrLoginServer>/azure-vote-front:v2
```

Dağıtımı izlemek için [kubectl get pod][kubectl-get] komutunu kullanın. Güncelleştirilmiş uygulama dağıtıldığında, podlarınız sonlandırılır ve yeni kapsayıcı görüntüsüyle yeniden oluşturulur.

```console
kubectl get pods
```

Aşağıdaki örnek çıktı, dağıtımın ilerlemesi sırasında sonlandırılan podları ve çalışan yeni örnekleri göstermektedir:

```
$ kubectl get pods

NAME                               READY     STATUS        RESTARTS   AGE
azure-vote-back-2978095810-gq9g0   1/1       Running       0          5m
azure-vote-front-1297194256-tpjlg  1/1       Running       0          1m
azure-vote-front-1297194256-tptnx  1/1       Running       0          5m
azure-vote-front-1297194256-zktw9  1/1       Terminating   0          1m
```

## <a name="test-the-updated-application"></a>Güncelleştirilmiş uygulamayı test etme

Güncelleştirme uygulamasını görüntülemek için öncelikle `azure-vote-front` hizmetinin dış IP'sini alın:

```console
kubectl get service azure-vote-front
```

Artık hizmetinizin IP adresi için bir yerel web tarayıcısı açın:

![Azure’da Kubernetes kümesinin görüntüsü](media/container-service-kubernetes-tutorials/vote-app-updated-external.png)

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, bir uygulamayı güncelleştirdiniz ve bu güncelleştirmeyi, AKS kümesine sundunuz. Şunları öğrendiniz:

> [!div class="checklist"]
> * Ön uç uygulama kodunu güncelleştirme
> * Güncelleştirilmiş kapsayıcı görüntüsü oluşturma
> * Kapsayıcı görüntüsünü Azure Container Registry’ye gönderme
> * Güncelleştirilmiş kapsayıcı görüntüsünü dağıtma

Bir AKS kümesini yeni bir Kubernetes sürümüne yükseltmeyi öğrenmek için bir sonraki öğreticiye geçin.

> [!div class="nextstepaction"]
> [Kubernetes'i yükseltme][aks-tutorial-upgrade]

<!-- LINKS - external -->
[docker-compose]: https://docs.docker.com/compose/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-set]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#set

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-upgrade]: ./tutorial-kubernetes-upgrade-cluster.md
[az-acr-login]: /cli/azure/acr
[azure-cli-install]: /cli/azure/install-azure-cli
