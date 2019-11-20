---
title: Azure Application Gateway giriş denetleyicisi nedir?
description: Bu makalede Application Gateway giriş denetleyicisinin ne olduğuna ilişkin bir giriş sunulmaktadır.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 9635798720667e38a767f26fc2e5f5374e420059
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73795544"
---
# <a name="what-is-application-gateway-ingress-controller"></a>Application Gateway giriş denetleyicisi nedir?
Application Gateway giriş denetleyicisi (AGIC), [Azure Kubernetes hizmeti (AKS](https://azure.microsoft.com/services/kubernetes-service/) ) müşterilerinin Azure 'un yerel [Application Gateway](https://azure.microsoft.com/services/application-gateway/) L7 yük dengeleyicisinden bulut yazılımlarını kullanıma sunmasına olanak sağlayan bir Kubernetes uygulamasıdır Internet. AGIC, üzerinde barındırdığı Kubernetes kümesini izler ve bir Application Gateway sürekli olarak güncelleştirir, böylece seçili hizmetler Internet 'e sunulur.

Giriş denetleyicisi, müşterinin AKS 'i üzerinde kendi Pod 'iyle çalışır. AGIC, değişiklikler için Kubernetes kaynaklarının bir alt kümesini izler. AKS kümesinin durumu belirli bir yapılandırmaya Application Gateway çevrilir ve [Azure Resource Manager (ARM)](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)uygulanır.

## <a name="benefits-of-application-gateway-ingress-controller"></a>Application Gateway giriş denetleyicisinin avantajları
AGIC, dağıtımınızın tek bir Application Gateway giriş denetleyicisiyle birden çok AKS kümesini denetlemesine olanak tanır. AGIC Ayrıca AKS kümesinin önünde başka bir yük dengeleyici/genel IP 'si olması gereğini ortadan kaldırmaya yardımcı olur ve isteklerin AKS kümesine ulaşması için veri yolunuzda birden çok sıçramayı önler. Application Gateway, özel IP 'leri doğrudan kullanarak ve NodePort ya da KubeProxy Hizmetleri gerektirmeyen bir düğüm ile iletişim yapmaz. Bu, dağıtımlarınız için daha iyi performans da getirir.

Giriş denetleyicisi yalnızca Standard_v2 ve WAF_v2 SKU 'Ları tarafından desteklenir ve bu da otomatik ölçeklendirme avantajları sağlar. Application Gateway, AKS kümenizdeki kaynakları tüketmeksizin trafik yüküyle ilgili bir artış veya azalmaya yanıt verebilir.

AGIC 'e ek olarak Application Gateway kullanmak, TLS ilkesi ve Web uygulaması güvenlik duvarı (WAF) işlevselliği sağlayarak AKS kümenizi korumanıza de yardımcı olur.

![Azure Application Gateway + AKS](./media/application-gateway-ingress-controller-overview/architecture.png)

AGIC, hizmet ve dağıtımlar/pods ile birlikte Kubernetes giriş [kaynağı](http://kubernetes.io/docs/user-guide/ingress/)aracılığıyla yapılandırılır. Azure 'un yerel Application Gateway L7 yük dengeleyicisinden yararlanarak birçok özellik sağlar. Birkaç ad vermek için:
  - URL yönlendirme
  - Tanımlama bilgisi tabanlı benzeşim
  - SSL sonlandırma
  - Uçtan uca SSL
  - Ortak, özel ve karma Web siteleri için destek
  - Tümleşik web uygulaması güvenlik duvarı

AGIC, birden çok ad alanını işleyebilir ve ProhibitedTargets sahiptir; bu da AGIC, diğer mevcut arka uçları etkilemeden AKS kümelerinde özel olarak Application Gateway yapılandırabilir. 

## <a name="next-steps"></a>Sonraki Adımlar

- [**Doğa alanı dağıtımı**](ingress-controller-install-new.md): boş-kurşun altyapısına AGIC, aks ve Application Gateway yükleme yönergeleri.
- [**Brownfield dağıtımı**](ingress-controller-install-existing.md): mevcut aks ve Application Gateway için agic 'yi yükler.

