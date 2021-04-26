---
title: Azure Container Registry Azure Kubernetes hizmeti ile tümleştirme
description: Azure Kubernetes Service 'i (AKS) Azure Container Registry (ACR) ile tümleştirmeyi öğrenin
services: container-service
manager: gwallace
ms.topic: article
ms.date: 01/08/2021
ms.openlocfilehash: ab8065a14aac9e798bfe7d632aa5b33c44706190
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775837"
---
# <a name="authenticate-with-azure-container-registry-from-azure-kubernetes-service"></a>Azure Kubernetes Service'ten Azure Container Registry ile Kimlik Doğrulaması Yapma

Azure Kubernetes Service (AKS) ile Azure Container Registry (ACR) kullanırken, bir kimlik doğrulama mekanizmasının kurulması gerekir. Bu işlem, ACR 'nize gerekli izinler verilerek CLı ve Portal deneyiminin bir parçası olarak uygulanır. Bu makalede, bu iki Azure hizmeti arasında kimlik doğrulaması yapılandırmak için örnekler sağlanmaktadır. 

Azure CLı ile birkaç basit komutlarda ACR tümleştirmesi için AKS tümleştirmesini ayarlayabilirsiniz. Bu tümleştirme, AKS kümesiyle ilişkili yönetilen kimliğe AcrPull rolünü atar.

> [!NOTE]
> Bu makale, AKS ve ACR arasındaki otomatik kimlik doğrulamasını içerir. Özel bir dış kayıt defterinden görüntü çekmeniz gerekiyorsa, bir [resim çekme gizli anahtarı][Image Pull Secret]kullanın.

## <a name="before-you-begin"></a>Başlamadan önce

Bu örneklerde şunlar gerekir:

* **Azure aboneliğinde** **sahip** veya **Azure Hesap Yöneticisi** rolü
* Azure CLı sürüm 2.7.0 veya üzeri

Bir **sahip** veya **Azure Hesap Yöneticisi** rolüne gerek duymamak için, yönetilen bir kimliği El Ile yapılandırabilir veya aks 'ten ACR 'nin kimliğini doğrulamak için mevcut bir yönetilen kimliği kullanabilirsiniz. Daha fazla bilgi için bkz. Azure [Container Registry 'de kimlik doğrulamak Için Azure yönetilen kimliği kullanma](../container-registry/container-registry-authentication-managed-identity.md).

## <a name="create-a-new-aks-cluster-with-acr-integration"></a>ACR tümleştirmesi ile yeni bir AKS kümesi oluşturma

AKS kümenizi ilk oluşturma sırasında AKS ve ACR tümleştirmesini ayarlayabilirsiniz.  AKS kümesinin ACR ile etkileşime geçmesini sağlamak için Azure Active Directory **yönetilen bir kimlik** kullanılır. Aşağıdaki CLı komutu aboneliğinizdeki mevcut bir ACR 'ye yetki vermenize ve yönetilen kimlik için uygun **Acrpull** rolünü yapılandırmanıza olanak tanır. Aşağıdaki parametreleriniz için geçerli değerler sağlayın.

```azurecli
# set this to the name of your Azure Container Registry.  It must be globally unique
MYACR=myContainerRegistry

# Run the following line to create an Azure Container Registry if you do not already have one
az acr create -n $MYACR -g myContainerRegistryResourceGroup --sku basic

# Create an AKS cluster with ACR integration
az aks create -n myAKSCluster -g myResourceGroup --generate-ssh-keys --attach-acr $MYACR
```

Alternatif olarak, aşağıdaki biçime sahip bir ACR kaynak KIMLIĞI kullanarak ACR adını belirtebilirsiniz:

`/subscriptions/\<subscription-id\>/resourceGroups/\<resource-group-name\>/providers/Microsoft.ContainerRegistry/registries/\<name\>`

> [!NOTE]
> AKS kümenizdeki farklı bir abonelikte bulunan bir ACR kullanıyorsanız, AKS kümesinden iliştirme veya bu kümeden ayrılırken ACR kaynak KIMLIĞINI kullanın.

```azurecli
az aks create -n myAKSCluster -g myResourceGroup --generate-ssh-keys --attach-acr /subscriptions/<subscription-id>/resourceGroups/myContainerRegistryResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry
```

Bu adımın tamamlanması birkaç dakika sürebilir.

## <a name="configure-acr-integration-for-existing-aks-clusters"></a>Mevcut AKS kümeleri için ACR tümleştirmesini yapılandırma

Aşağıdaki gibi **ACR-Name** veya **ACR-Resource-id** için geçerli değerler sağlayarak mevcut bir ACR 'yi mevcut aks kümeleriyle tümleştirin.

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acr-name>
```

veya

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acr-resource-id>
```

Bir ACR ve AKS kümesi arasındaki tümleştirmeyi aşağıdakiler ile de kaldırabilirsiniz

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --detach-acr <acr-name>
```

veya

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --detach-acr <acr-resource-id>
```

## <a name="working-with-acr--aks"></a>ACR & AKS ile çalışma

### <a name="import-an-image-into-your-acr"></a>ACR 'nize görüntü aktarma

Aşağıdaki çalıştırarak, Docker Hub 'ından bir görüntüyü ACR 'nize aktarın:


```azurecli
az acr import  -n <acr-name> --source docker.io/library/nginx:latest --image nginx:v1
```

### <a name="deploy-the-sample-image-from-acr-to-aks"></a>Örnek görüntüyü ACR 'den AKS 'e dağıtma

Uygun AKS kimlik bilgilerine sahip olduğunuzdan emin olun

```azurecli
az aks get-credentials -g myResourceGroup -n myAKSCluster
```

Aşağıdakileri içeren **ACR-NGINX. YAML** adlı bir dosya oluşturun. **ACR-Name** için kayıt defterinizin kaynak adını değiştirin. Örnek: *Mycontainerregistry*.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx0-deployment
  labels:
    app: nginx0-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx0
  template:
    metadata:
      labels:
        app: nginx0
    spec:
      containers:
      - name: nginx
        image: <acr-name>.azurecr.io/nginx:v1
        ports:
        - containerPort: 80
```

Ardından, bu dağıtımı AKS kümenizde çalıştırın:

```console
kubectl apply -f acr-nginx.yaml
```

Aşağıdakileri çalıştırarak dağıtımı izleyebilirsiniz:

```console
kubectl get pods
```

İki çalışan bir pod olmalıdır.

```output
NAME                                 READY   STATUS    RESTARTS   AGE
nginx0-deployment-669dfc4d4b-x74kr   1/1     Running   0          20s
nginx0-deployment-669dfc4d4b-xdpd6   1/1     Running   0          20s
```

### <a name="troubleshooting"></a>Sorun giderme
* Kayıt defterinin AKS kümesinden erişilebilir olduğunu doğrulamak için [az aks Check-ACR](/cli/azure/aks#az_aks_check_acr) komutunu çalıştırın.
* [ACR tanılama](../container-registry/container-registry-diagnostics-audit-logs.md) hakkında daha fazla bilgi edinin
* [ACR sistem durumu](../container-registry/container-registry-check-health.md) hakkında daha fazla bilgi edinin

<!-- LINKS - external -->
[AKS AKS CLI]: /cli/azure/aks#az_aks_create
[Image Pull secret]: https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/