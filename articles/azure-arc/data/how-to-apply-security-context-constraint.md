---
title: Güvenlik bağlamı kısıtlamasını uygulama
description: Azure Red Hat OpenShift veya Red Hat OpenShift kapsayıcı platformu için bir güvenlik bağlamı kısıtlaması uygulama
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
ms.date: 01/15/2021
ms.topic: how-to
ms.openlocfilehash: a6751a9ff96b82f9d4fdaacd449c8a766afaa381
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98697844"
---
# <a name="apply-a-security-context-constraint-for-azure-arc-enabled-data-services-on-openshift"></a>OpenShift üzerinde Azure Arc etkin veri Hizmetleri için bir güvenlik bağlamı kısıtlaması uygulama

Bu makalede, Azure Arc etkin veri Hizmetleri için bir güvenlik bağlamı kısıtlamasının nasıl uygulanacağı açıklanır. 

## <a name="applicability"></a>Uygulanabilirlik

Azure Red Hat OpenShift veya Red Hat OpenShift kapsayıcı platformunda dağıtımlar için geçerlidir. 

## <a name="apply-security-context-constraint"></a>Güvenlik bağlamı kısıtlaması Uygula

[!INCLUDE [apply-security-context-constraint](includes/apply-security-context-constraint.md)]

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Arc veri denetleyicisi oluşturma](create-data-controller.md)
- [Azure Data Studio veri denetleyicisi oluşturma](create-data-controller-azure-data-studio.md)
- [Azure Arc veri denetleyicisi 'ni kullanarak oluşturma [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]](create-data-controller-using-azdata.md)

