---
title: Azure Dev Spaces etkinleştirilmiş bir Kubernetes kümesi oluşturma-Azure Cloud Shell
services: azure-dev-spaces
ms.date: 10/04/2018
ms.topic: conceptual
description: Her şeyi yüklemeden doğrudan tarayıcınızdan Azure Dev Spaces için etkin bir Kubernetes kümesini kolayca oluşturmayı öğrenin.
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes hizmeti, kapsayıcılar, Held, hizmet ağı, hizmet kafesi yönlendirme, kubectl, k8s
ms.openlocfilehash: ce73f46a2451dfa87751d90f9cd6b31d5c80683f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91963559"
---
# <a name="create-a-kubernetes-cluster-with-azure-dev-spaces-enabled-with-azure-cloud-shell"></a>Azure Cloud Shell ile Azure Dev Spaces etkin bir Kubernetes kümesi oluşturma

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

Bu sayfadan **deneyin** düğmesini kullanarak bir Azure Kubernetes hizmet kümesi oluşturmak için [Azure Cloud Shell](/azure/cloud-shell) kullanabilirsiniz. Oturum açmadıysanız, bir Azure hesabıyla oturum açmak için istemleri izleyin ve sonra Azure Cloud Shell istemine komutları yazın.

## <a name="create-the-cluster"></a>Kümeyi oluşturma

İlk olarak, [Azure dev Spaces destekleyen bir bölgede][supported-regions]kaynak grubunu oluşturun.

```azurecli-interactive
az group create --name MyResourceGroup --location <region>
```

Şu komutu kullanarak bir Kubernetes kümesi oluşturun:

```azurecli-interactive
az aks create -g MyResourceGroup -n MyAKS --location <region> --generate-ssh-keys
```

Kümenin oluşturulması birkaç dakika sürer.  Tamamlandığında, çıktı JSON biçiminde gösterilir. Arama yapın `provisioningState` ve doğrulayın `Succeeded` .

## <a name="next-steps"></a>Sonraki adımlar

Tam öğreticilerin bağlantıları için bkz. [Azure dev Spaces](../index.yml) .

> [!IMPORTANT]
> Azure Dev Spaces hızlı başlangıç ve öğreticilerin birçoğu, işlemleri gerçekleştirmek için Azure Dev Spaces CLı kullanır. Azure Dev Spaces CLı 'yi Azure Cloud Shell yükleyemezsiniz.


[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
