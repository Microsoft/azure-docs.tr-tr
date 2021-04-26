---
title: AKS üzerinde Azure Dev Spaces etkinleştirme & istemci tarafı araçları 'nı yükler
services: azure-dev-spaces
ms.date: 07/24/2019
ms.topic: conceptual
description: AKS kümesinde Azure Dev Spaces etkinleştirmeyi ve istemci tarafı araçları yüklemeyi öğrenin.
ms.custom: devx-track-azurecli
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes hizmeti, kapsayıcılar, Held, hizmet ağı, hizmet kafesi yönlendirme, kubectl, k8s
ms.openlocfilehash: 079a9e1b28b315457ac20d3aa9e7d29ce28fa077
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107505369"
---
# <a name="enable-azure-dev-spaces-on-an-aks-cluster-and-install-the-client-side-tools"></a>AKS kümesinde Azure Dev Spaces etkinleştirme ve istemci tarafı araçları 'nı yüklemeye

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

Bu makalede, bir AKS kümesinde Azure Dev Spaces etkinleştirmenin yanı sıra istemci tarafı araçları yüklemek için kullanabileceğiniz çeşitli yollar gösterilmektedir.

## <a name="enable-azure-dev-spaces-using-the-azure-cli"></a>Azure CLı kullanarak Azure Dev Spaces etkinleştirme

CLı kullanarak dev alanlarını etkinleştirebilmeniz için şunlar gerekir:
* Azure aboneliği. Azure aboneliğiniz yoksa [ücretsiz hesap][az-portal-create-account] oluşturabilirsiniz.
* [Azure CLI yüklendi][install-cli].
* [Desteklenen bir bölgedeki][supported-regions] [aks kümesi][create-aks-cli] .

`use-dev-spaces`AKS kümenizde dev alanlarını etkinleştirmek ve istemleri izlemek için komutunu kullanın.

```azurecli
az aks use-dev-spaces -g myResourceGroup -n myAKSCluster
```

Yukarıdaki komut, *Myresourcegroup* grubundaki *Myakscluster* kümesinde dev alanları sunar ve *varsayılan* bir dev alanı oluşturur.

```console
'An Azure Dev Spaces Controller' will be created that targets resource 'myAKSCluster' in resource group 'myResourceGroup'. Continue? (y/N): y

Creating and selecting Azure Dev Spaces Controller 'myAKSCluster' in resource group 'myResourceGroup' that targets resource 'myAKSCluster' in resource group 'myResourceGroup'...2m 24s

Select a dev space or Kubernetes namespace to use as a dev space.
 [1] default
Type a number or a new name: 1

Kubernetes namespace 'default' will be configured as a dev space. This will enable Azure Dev Spaces instrumentation for new workloads in the namespace. Continue? (Y/n): Y

Configuring and selecting dev space 'default'...3s

Managed Kubernetes cluster 'myAKSCluster' in resource group 'myResourceGroup' is ready for development in dev space 'default'. Type `azds prep` to prepare a source directory for use with Azure Dev Spaces and `azds up` to run.
```

`use-dev-spaces`Komut ayrıca Azure dev Spaces CLI 'yi de yüklüyor.

## <a name="install-the-client-side-tools"></a>İstemci tarafı araçları 'nı yükler

Yerel makinenizden bir AKS kümesindeki dev Spaces ile etkileşim kurmak için Azure Dev Spaces istemci tarafı araçlarını kullanabilirsiniz. İstemci tarafı araçları yüklemek için çeşitli yollar vardır:

* [Visual Studio Code][vscode], [Azure dev Spaces uzantısını][vscode-extension]yükler.
* [Visual Studio 2019][visual-studio]' de Azure geliştirme iş yükünü yükledikten sonra.
* [Windows][cli-win], [Mac][cli-mac]veya [Linux][cli-linux] CLI 'yı indirip yükleyin.

## <a name="remove-azure-dev-spaces-using-the-azure-cli"></a>Azure CLı kullanarak Azure Dev Spaces kaldırma

AKS kümenizdeki Azure Dev Spaces kaldırmak için `azds remove` komutunu kullanın.

```azurecli
azds remove -g MyResourceGroup -n MyAKS
```

Aşağıdaki örnek çıktıda Azure Dev Spaces *Myaks* kümesinden kaldırma gösterilmektedir.

```azurecli
$ azds remove -g MyResourceGroup -n MyAKS
Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAKS' in resource group 'MyResourceGroup' will be deleted. This will remove Azure Dev Spaces instrumentation from the target resource for new workloads. Continue? (y/N): y

Deleting Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAks' in resource group 'MyResourceGroup' (takes a few minutes)...
```

Azure Dev Spaces ile oluşturduğunuz herhangi bir ad alanı, iş yükleriyle birlikte kalacaktır, ancak bu ad alanlarındaki yeni iş yükleri Azure Dev Spaces birlikte görünmez. Ayrıca, Azure Dev Spaces ile işaretlenmiş mevcut tüm yığınları yeniden başlatırsanız, hatalar görebilirsiniz. Bu FID 'ler Azure Dev Spaces araçları olmadan yeniden dağıtılmalıdır. Azure Dev Spaces kümenizdeki tamamen kaldırmak için, Azure Dev Spaces etkinleştirildiği tüm ad alanlarındaki tüm kümelerinizi silin.

## <a name="next-steps"></a>Sonraki adımlar

Azure Dev Spaces nasıl çalıştığı hakkında daha fazla bilgi edinin.

> [!div class="nextstepaction"]
> [Azure Dev Spaces nasıl çalışır?](../how-dev-spaces-works.md)

[create-aks-cli]: ../../aks/kubernetes-walkthrough.md#create-a-resource-group
[install-cli]: /cli/azure/install-azure-cli
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[az-portal]: https://portal.azure.com
[az-portal-create-account]: https://azure.microsoft.com/free
[cli-linux]: https://aka.ms/get-azds-linux
[cli-mac]: https://aka.ms/get-azds-mac
[cli-win]: https://aka.ms/get-azds-windows
[visual-studio]: https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs
[visual-studio-k8s-tools]: https://aka.ms/get-vsk8stools
[vscode]: https://code.visualstudio.com/download
[vscode-extension]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
