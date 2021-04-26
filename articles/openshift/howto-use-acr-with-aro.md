---
title: Azure Red Hat OpenShift ile Azure Container Registry kullanma
description: Azure Red Hat OpenShift kümenize Azure Container Registry bir kapsayıcı çekmeyi ve çalıştırmayı öğrenin.
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 01/10/2021
ms.openlocfilehash: 651b73db084e8090f59faeffa9991c2ac468ca08
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100634422"
---
# <a name="use-azure-container-registry-with-azure-red-hat-openshift-aro"></a>Azure Red Hat OpenShift (ARO) ile Azure Container Registry kullanma

Azure Container Registry (ACR), coğrafi çoğaltma gibi kurumsal yeteneklere sahip özel Docker kapsayıcı görüntülerini depolamak için kullanabileceğiniz bir yönetilen kapsayıcı kayıt defteri hizmetidir. Bir ARO kümesinden ACR 'ye erişmek için küme, Docker oturum açma kimlik bilgilerini bir Kubernetes gizli dosyasında depolayarak ACR ile kimlik doğrulaması yapabilir.  Benzer şekilde, bir ARO kümesi, görüntüyü çekerek kayıt defterine göre kimlik doğrulaması yapmak için pod belirtiminde bir ımagepullsecret kullanabilir. Bu makalede, özel Docker kapsayıcı görüntülerini depolamak ve çekmek için Azure Red Hat OpenShift kümesiyle bir Azure Container Registry ayarlamayı öğreneceksiniz.

## <a name="prerequisites"></a>Önkoşullar

Bu kılavuzda, mevcut bir Azure Container Registry olduğunu varsaymaktadır. Bunu yapmazsanız, bir kapsayıcı kayıt defteri oluşturmak için Azure portal veya [Azure CLI yönergelerini](../container-registry/container-registry-get-started-azure-cli.md) kullanın.

Bu makalede ayrıca var olan bir Azure Red Hat OpenShift kümeniz olduğunu ve CLI 'nın yüklü olduğunu varsaymaktadır `oc` . Aksi takdirde, [Aro kümesi oluşturma öğreticisindeki](tutorial-create-cluster.md)yönergeleri izleyin.

## <a name="get-a-pull-secret"></a>Kısa bir gizli dizi alma

ACR 'den, ARO kümenizdeki kayıt defterine erişmek için bir çekme gizli anahtarı gerekir.

Çekme gizli kimlik bilgilerinizi almak için Azure portal veya Azure CLı 'yi kullanabilirsiniz.

Azure portal kullanıyorsanız ACR örneğinize gidin ve **erişim anahtarları**' nı seçin.  `docker-username`Kapsayıcı kayıt defterinizin adıdır, için Password veya paSsWorD2 kullanın `docker-password` .

![Erişim tuşları](./media/acr-access-keys.png)

Bunun yerine, bu kimlik bilgilerini almak için Azure CLı 'yi kullanabilirsiniz:
```azurecli
az acr credential show -n <your registry name>
```

## <a name="create-the-kubernetes-secret"></a>Kubernetes gizliliğini oluşturma

Şimdi bu kimlik bilgilerini bir Kubernetes parolası oluşturacak şekilde kullanacağız. ACR kimlik bilgilerinizle aşağıdaki komutu yürütün:

```
oc create secret docker-registry \
    --docker-server=<your registry name>.azurecr.io \
    --docker-username=<your registry name> \
    --docker-password=******** \
    --docker-email=unused \
    acr-secret
```

>[!NOTE]
>Bu gizli dizi geçerli OpenShift projesinde (Kubernetes ad alanı) depolanacak ve yalnızca bu projede oluşturulan Pod tarafından referenceto edilecek.  Küme genelinde çekme gizli dizisi oluşturma hakkında daha fazla yönerge için bu [belgeye](https://docs.openshift.com/container-platform/4.4/openshift_images/managing_images/using-image-pull-secrets.html) bakın.

## <a name="create-a-pod-using-a-private-registry-image"></a>Özel bir kayıt defteri görüntüsü kullanarak Pod oluşturma

Şimdi, ARO kümenizi ACR 'nize bağladığımıza göre, bir pod oluşturmak için ACR 'nizden bir görüntü çekelim.

Bir pod belirtimi ile başlayın ve bir ımagepullsecret olarak oluşturduğunuz gizli anahtarı belirtin:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: hello-world
spec:
  containers:
  - name: hello-world
    image: <your registry name>.azurecr.io/hello-world:v1
  imagePullSecrets:
  - name: acr-secret
```

Pod **'nizin çalışır durumda** olduğunu test etmek için, bu komutu yürütün ve durum ' a kadar bekleyin:

```bash
$ oc get pods --watch
NAME         READY   STATUS    RESTARTS   AGE
hello-world  1/1     Running   0          30s
```

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Container Registry](../container-registry/container-registry-concepts.md)
* [Hızlı başlangıç: Azure CLı kullanarak özel kapsayıcı kayıt defteri oluşturma](../container-registry/container-registry-get-started-azure-cli.md)
