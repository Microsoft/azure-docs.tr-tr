---
title: Azure Güvenlik Merkezi ve Azure Kubernetes hizmeti | Microsoft Docs
description: Azure Güvenlik Merkezi 'nin Azure Kubernetes hizmetleriyle tümleştirmesi hakkında bilgi edinin
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 4fc0654aab1c68888e86dc95de658bc69a01e02d
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74123985"
---
# <a name="azure-kubernetes-services-integration-with-security-center-preview"></a>Güvenlik Merkezi ile Azure Kubernetes Hizmetleri Tümleştirmesi (Önizleme)
Azure Kubernetes Service (AKS), Kapsayıcılı uygulamalar geliştirmeye, dağıtmaya ve yönetmeye yönelik Microsoft tarafından yönetilen bir hizmettir. 

AKS düğümleriniz, bulut trafiğiniz ve güvenlik denetimlerine daha derin bir görünürlük elde etmek için Azure Güvenlik Merkezi 'nin standart katmanı (bkz. [fiyatlandırma](security-center-pricing.md)) ile birlikte aks kullanın.

Güvenlik Merkezi, aks ana düğümü tarafından zaten toplanan verileri kullanarak AKS kümelerinize yönelik güvenlik avantajları sunar. 

![Azure Güvenlik Merkezi ve Azure Kubernetes hizmeti (AKS) üst düzey genel bakış](./media/azure-kubernetes-service-integration/aks-asc-integration-overview.png)

Bu iki araç birlikte, en iyi bulut Yerel Kubernetes güvenlik sunumunu oluşturur. 

## <a name="benefits-of-integration"></a>Tümleştirmenin avantajları

İki hizmetin birlikte kullanılması aşağıdakileri sağlar:

* **Güvenlik önerileri** : Güvenlik Merkezi, aks kaynaklarınızı tanımlar ve bunları kümeler halinde tek tek sanal makinelere kategorilere ayırır. Ardından, kaynak başına güvenlik önerilerini görüntüleyebilirsiniz. Daha fazla bilgi için bkz. [güvenlik önerilerini uygulama](security-center-recommendations.md). 

    > [!NOTE]
    > Bir güvenlik merkezi önerisinin adı "(Önizleme)" etiketiyle biterse, bu, önerinin önizleme yapısına işaret eder; özelliği değil.

* **Ortam sağlamlaştırma** -Güvenlik Merkezi, Kubernetes kümelerinizin yapılandırmasını sürekli izler ve sektör standartlarını yansıtan güvenlik önerileri oluşturur.

* **Çalışma zamanı koruması** -aşağıdaki aks kaynaklarının sürekli analizine sahip Güvenlik Merkezi, ana bilgisayar *ve* aks kümesi düzeyinde algılanan tehditler ve kötü amaçlı etkinlikler hakkında sizi uyarır (daha fazla bilgi için bkz. [Azure Container Service ](https://docs.microsoft.com/azure/security-center/security-center-alerts-compute#azure-containers-)):
    * Ağ verileri ve işlem oluşturma gibi ham güvenlik olayları
    * Kubernetes denetim günlüğü

![Azure Güvenlik Merkezi ve Azure Kubernetes hizmeti (AKS) daha ayrıntılı](./media/azure-kubernetes-service-integration/aks-asc-integration-detailed.png)

> [!NOTE]
> Azure Güvenlik Merkezi tarafından Kubernetes ortamınızdan taranan bazı veriler hassas bilgiler içerebilir.

## <a name="next-steps"></a>Sonraki adımlar

Güvenlik Merkezi 'nin kapsayıcı güvenlik özellikleri hakkında daha fazla bilgi edinmek için bkz.:

* [Azure Güvenlik Merkezi ve kapsayıcı güvenliği](container-security.md)

* [Azure Container Registry ile tümleştirme](azure-container-registry-integration.md)

* [Sanal makine koruması](security-center-virtual-machine-protection.md) -Güvenlik Merkezi 'nin önerilerini açıklar

* [Microsoft 'Ta veri yönetimi](https://www.microsoft.com/trust-center/privacy/data-management) -Microsoft hizmetlerinin (Azure, Intune ve Office 365 dahil) veri Ilkelerini, Microsoft 'un veri yönetiminin ayrıntılarını ve verilerinizi etkileyen bekletme ilkelerini açıklar