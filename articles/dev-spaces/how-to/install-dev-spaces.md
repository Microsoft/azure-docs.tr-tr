---
title: AKS 'e ve istemci tarafı araçlarına Azure Dev Spaces yüklemesi
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 07/24/2019
ms.topic: conceptual
description: Bir AKS kümesine Azure Dev Spaces yüklemeyi ve istemci tarafı araçları yüklemeyi öğrenin.
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes hizmeti, kapsayıcılar, Held, hizmet ağı, hizmet kafesi yönlendirme, kubectl, k8s
ms.openlocfilehash: 642d4406840f1748c3b6cbb4441227fff0c6d721
ms.sourcegitcommit: 5604661655840c428045eb837fb8704dca811da0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68494843"
---
# <a name="install-azure-dev-spaces-on-aks-and-the-client-side-tooling"></a>AKS 'e ve istemci tarafı araçlarına Azure Dev Spaces yüklemesi

Bu makalede, bir AKS kümesine Azure Dev Spaces yüklemek ve istemci tarafı araçları yüklemek için kullanabileceğiniz çeşitli yollar gösterilmektedir.

## <a name="install-azure-dev-spaces-using-the-cli"></a>CLı kullanarak Azure Dev Spaces yükleyip

CLı kullanarak dev alanlarını yüklemeden önce şunları yapmanız gerekir:
* Azure aboneliği. Azure aboneliğiniz yoksa [ücretsiz hesap][az-portal-create-account] oluşturabilirsiniz.
* [Azure CLI yüklendi][install-cli].
* [Desteklenen bir bölgedeki][supported-regions] [aks kümesi][create-aks-cli] .

Aks kümenizde dev alanlarını etkinleştirmek ve istemleri izlemek için komutunukullanın.`use-dev-spaces`

```cmd
az aks use-dev-spaces -g myResourceGroup -n myAKSCluster
```

Yukarıdaki komut, *Myresourcegroup* grubundaki *Myakscluster* kümesinde dev alanları sunar ve *varsayılan* bir dev alanı oluşturur.

```cmd
$ az aks use-dev-spaces -g myResourceGroup -n myAKSCluster

'An Azure Dev Spaces Controller' will be created that targets resource 'myAKSCluster' in resource group 'myResourceGroup'. Continue? (y/N): y

Creating and selecting Azure Dev Spaces Controller 'myAKSCluster' in resource group 'myResourceGroup' that targets resource 'myAKSCluster' in resource group 'myResourceGroup'...2m 24s

Select a dev space or Kubernetes namespace to use as a dev space.
 [1] default
Type a number or a new name: 1

Kubernetes namespace 'default' will be configured as a dev space. This will enable Azure Dev Spaces instrumentation for new workloads in the namespace. Continue? (Y/n): Y

Configuring and selecting dev space 'default'...3s

Managed Kubernetes cluster 'myAKSCluster' in resource group 'myResourceGroup' is ready for development in dev space 'default'. Type `azds prep` to prepare a source directory for use with Azure Dev Spaces and `azds up` to run.
```

`use-dev-spaces` Komut ayrıca Azure dev Spaces CLI 'yi de yüklüyor.

## <a name="install-azure-dev-spaces-using-the-azure-portal"></a>Azure portal kullanarak Azure Dev Spaces yüklemesi

Azure portal kullanarak dev alanlarını yükleyebilmek için önce şunları yapmanız gerekir:
* Azure aboneliği. Azure aboneliğiniz yoksa [ücretsiz hesap][az-portal-create-account] oluşturabilirsiniz.
* [Desteklenen bir bölgedeki][supported-regions] [aks kümesi][create-aks-portal] .

Azure portal kullanarak Azure Dev Spaces yüklemek için:
1. [Azure Portal][az-portal] oturum açın.
1. AKS kümenize gidin.
1. *Geliştirme alanları*' na tıklayın.
1. Değişiklik *geliştirme alanlarını* *Evet* olarak etkinleştirin ve *Kaydet*' e tıklayın.

![Azure portal dev alanlarını etkinleştirme](../media/how-to-setup-dev-spaces/enable-dev-spaces-portal.png)

**Azure Portal kullanarak** Azure Dev Spaces yüklemek Azure dev Spaces için herhangi bir istemci tarafı aracı yüklemez.

## <a name="install-the-client-side-tooling"></a>İstemci tarafı araçları 'nı yükler

Yerel makinenizden bir AKS kümesinde dev Spaces ile etkileşim kurmak için Azure Dev Spaces istemci tarafı araçları 'nı kullanabilirsiniz. İstemci tarafı araçları 'nı yüklemenin çeşitli yolları vardır:

* [Visual Studio Code][vscode], [Azure dev Spaces uzantısını][vscode-extension]yükler.
* [Visual Studio 2019][visual-studio]' de Azure geliştirme iş yükünü yükledikten sonra.
* Visual Studio 2017 ' de Web geliştirme iş yükünü ve [Kubernetes için Visual Studio Araçları][visual-studio-k8s-tools]yüklemesini yapın.
* [Windows][cli-win], [Mac][cli-mac]veya [Linux][cli-linux] CLI 'yı indirip yükleyin.

## <a name="next-steps"></a>Sonraki adımlar

Azure Dev Spaces birden çok kapsayıcı genelinde daha karmaşık uygulamalar geliştirmenize nasıl yardımcı olduğunu ve farklı alanlarda kodunuzun farklı sürümleriyle veya dallarıyla çalışarak işbirliğine dayalı geliştirmeyi nasıl kolaylaştırabileceğinizi öğrenin.

> [!div class="nextstepaction"]
> [Azure Dev Spaces 'de takım geliştirme][team-development-qs]

[create-aks-cli]: ../../aks/kubernetes-walkthrough.md#create-a-resource-group
[create-aks-portal]: ../../aks/kubernetes-walkthrough-portal.md#create-an-aks-cluster
[install-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[supported-regions]: ../about.md#supported-regions-and-configurations
[team-development-qs]: ../quickstart-team-development.md

[az-portal]: https://portal.azure.com
[az-portal-create-account]: https://azure.microsoft.com/free
[cli-linux]: https://aka.ms/get-azds-linux
[cli-mac]: https://aka.ms/get-azds-mac
[cli-win]: https://aka.ms/get-azds-windows
[visual-studio]: https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs
[visual-studio-k8s-tools]: https://aka.ms/get-vsk8stools
[vscode]: https://code.visualstudio.com/download
[vscode-extension]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
