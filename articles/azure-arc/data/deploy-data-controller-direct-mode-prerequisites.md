---
title: Önkoşullar | Doğrudan bağlama modu
description: Veri denetleyicisini doğrudan bağlanma modunda dağıtmaya yönelik önkoşullar.
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 03/31/2021
ms.topic: overview
ms.openlocfilehash: 940c08dc56e8b0f2217e556da1af31b44ceeda0a
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107716321"
---
# <a name="deploy-data-controller---direct-connect-mode-prerequisites"></a>Veri denetleyicisi dağıtma-doğrudan bağlantı modu (Önkoşullar)

Bu makalede, Azure Arc etkin veri Hizmetleri için bir veri denetleyicisini doğrudan bağlanma modunda dağıtmaya nasıl hazırlanılacağı açıklanır.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

Yüksek düzeyde bir özette önkoşulları şunlardır:

1. Araçları yükleme
1. Uzantılar ekleme
1. Hizmet sorumlusu oluşturma ve ölçümler için rolleri yapılandırma
1. Azure Arc etkin Kubernetes kullanarak Kubernetes kümesini Azure 'a bağlama

Bu önkoşulları tamamladıktan sonra, [Azure Arc Data Controller 'ı dağıtabilirsiniz | Doğrudan bağlanma modu](deploy-data-controller-direct-mode.md).

Bu makalenin geri kalan bölümleri önkoşulları belirler.

## <a name="install-tools"></a>Araçları yükleme

- Helb sürüm 3.3 + ([Install](https://helm.sh/docs/intro/install/))
- Azure CLı ([Install](/sql/azdata/install/deploy-install-azdata))

## <a name="add-extensions-for-azure-cli"></a>Azure CLı için uzantı ekleme

Ayrıca, aşağıdaki az uzantı de gereklidir:
- Azure CLı `k8s-extension` uzantısı (0.2.0)
- Azure CLı `customlocation` (0.1.0)

Örnek `az` ve CLI uzantıları şöyle olabilir:

```console
$ az version
{
  "azure-cli": "2.19.1",
  "azure-cli-core": "2.19.1",
  "azure-cli-telemetry": "1.0.6",
  "extensions": {
    "connectedk8s": "1.1.0",
    "customlocation": "0.1.0",
    "k8s-configuration": "1.0.0",
    "k8s-extension": "0.2.0"
  }
}
```

## <a name="create-service-principal-and-configure-roles-for-metrics"></a>Hizmet sorumlusu oluşturma ve ölçümler için rolleri yapılandırma

[Ölçümleri karşıya yükleme](upload-metrics-and-logs-to-azure-monitor.md) makalesinde açıklanan adımları izleyin ve bir hizmet sorumlusu oluşturun ve rolleri makalede açıklandığı gibi verin. 

[Azure Arc veri denetleyicisi DAĞıTıRKEN](deploy-data-controller-direct-mode.md)SPN ClientID, Tenantıd ve istemci gizli bilgileri gerekir. 

## <a name="connect-kubernetes-cluster-to-azure-using-azure-arc-enabled-kubernetes"></a>Azure Arc etkin Kubernetes kullanarak Kubernetes kümesini Azure 'a bağlama

Bu görevi gerçekleştirmek için, [var olan bir Kubernetes kümesini Azure yaya bağlama](../kubernetes/quickstart-connect-cluster.md)bölümündeki adımları izleyin.

## <a name="next-steps"></a>Sonraki adımlar

Bu önkoşulları tamamladıktan sonra, [Azure Arc Data Controller 'ı dağıtabilirsiniz | Doğrudan bağlanma modu](deploy-data-controller-direct-mode.md).
