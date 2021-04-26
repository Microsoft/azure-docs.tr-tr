---
title: Azure Dev Spaces ile kubectl kullanma
services: azure-dev-spaces
ms.date: 05/11/2018
ms.topic: conceptual
description: Azure Dev Spaces etkin olan bir Azure Kubernetes hizmet kümesinde kubectl komutlarını bir geliştirme alanı içinde kullanmayı öğrenin
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes hizmeti, kapsayıcılar, Held, hizmet ağı, hizmet kafesi yönlendirme, kubectl, k8s '
ms.openlocfilehash: e6f79d98cf209d1bc19753f19c9b17b06017c2b7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91960178"
---
# <a name="use-kubectl-with-an-azure-dev-space"></a>Azure dev Space ile kubectl kullanma

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

Kubernetes kümesine bir Azure dev alanı içinde erişebilir ve gibi mevcut Kubernetes araçlarını kullanabilirsiniz `kubectl` .

Çalışan `az aks use-dev-spaces` komut sizin için otomatik olarak bir `kubectl` yapılandırma bağlamı ekler. bu nedenle, kubectl Azure dev Spaces Kubernetes kümenize zaten bağlanmalıdır. Örnekler:
- Geçerli bağlamı onaylayın: `kubectl config current-context`
- Tüm kullanılabilir bağlamları Listele: `kubectl config get-contexts` . 
- Bağlamı Değiştir: `kubectl config use-context <context-name>`
- Kubernetes panosunu görüntüleyin: çalıştırın `kubectl proxy` , ardından tarayıcınızı bu komutun yaydığı adrese açın ( `/ui` Kubernetes panosuna gıtmek için URL 'ye ekleyin).
- Çalışan *Hizmetleri varsayılan Azure dev Spaces alanında varsayılan adı listeleyin*: `kubectl get services --namespace=default`

