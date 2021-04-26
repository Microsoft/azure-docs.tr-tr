---
title: Azure Güvenlik Merkezi için yerleşik ilke tanımları
description: Azure Güvenlik Merkezi için Azure Ilkesi yerleşik ilke tanımlarını listeler. Bu yerleşik ilke tanımları, Azure kaynaklarınızı yönetmek için yaygın yaklaşımlar sağlar.
ms.date: 04/21/2021
ms.topic: reference
author: memildin
ms.author: memildin
ms.service: security-center
ms.custom: subject-policy-reference
ms.openlocfilehash: 0fc5e2ed183020f32b59fec7f4436754467968e0
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107872438"
---
# <a name="azure-policy-built-in-definitions-for-azure-security-center"></a>Azure Güvenlik Merkezi için Azure İlkesi yerleşik tanımları

Bu sayfa, Azure Güvenlik Merkezi ile ilgili [Azure ilkesi](../governance/policy/overview.md) yerleşik ilke tanımlarının bir dizinidir. Aşağıdaki ilke tanımlarının gruplandırmaları kullanılabilir:

- [Girişimler](#azure-security-center-initiatives) grubu, "Güvenlik Merkezi" kategorisindeki Azure ilke girişim tanımlarını listeler.
- [Varsayılan girişim](#azure-security-center-initiatives) grubu, güvenlik merkezi 'nin varsayılan girişim, [Azure Güvenlik kıyaslamalarından](https://docs.microsoft.com/security/benchmark/azure/introduction)oluşan tüm Azure ilke tanımlarını listeler. Bu Microsoft tarafından yazılmış, yaygın olarak kullanılan kıyaslanmalar, [Internet güvenliği (CIS) Için merkezden](https://www.cisecurity.org/benchmark/azure/) ve [ulusal standartlar ve teknolojı Enstitüsü (NIST)](https://www.nist.gov/) ile bulut merkezli güvenliğe odaklanarak yapılar oluşturur.
- [Kategori](#azure-security-center-category) grubu, "Güvenlik Merkezi" kategorisindeki tüm Azure ilke tanımlarını listeler.

Güvenlik ilkeleri hakkında daha fazla bilgi için bkz. [güvenlik Ilkeleriyle çalışma](./tutorial-security-policy.md). Diğer hizmetlere yönelik diğer Azure Ilkesi için yerleşik olarak [bulunan Azure ilkesi yerleşik tanımları](../governance/policy/samples/built-in-policies.md)bölümüne bakın.

Her yerleşik ilke tanımının adı Azure portal ilke tanımına bağlanır. Kaynağı [Azure Policy GitHub](https://github.com/Azure/azure-policy)deposunda görüntülemek için **Sürüm** sütunundaki bağlantıyı kullanın.

## <a name="azure-security-center-initiatives"></a>Azure Güvenlik Merkezi girişimleri

Güvenlik Merkezi tarafından izlenen yerleşik girişimler hakkında bilgi edinmek için aşağıdaki tabloya bakın:

[!INCLUDE [azure-policy-reference-policyset-security-center](../../includes/policy/reference/bycat/policysets-security-center.md)]

## <a name="security-centers-default-initiative-azure-security-benchmark"></a>Güvenlik Merkezi 'nin varsayılan girişimi (Azure Güvenlik kıyaslaması)

Güvenlik Merkezi tarafından izlenen yerleşik ilkeler hakkında bilgi edinmek için aşağıdaki tabloya bakın:

[!INCLUDE [azure-policy-reference-init-asc](../../includes/policy/reference/custom/init-asc.md)]

## <a name="azure-security-center-category"></a>Azure Güvenlik Merkezi kategorisi

[!INCLUDE [azure-policy-reference-category-securitycenter](../../includes/policy/reference/bycat/policies-security-center.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, güvenlik merkezi 'nde Azure Ilkesi güvenlik ilkesi tanımları hakkında bilgi edindiniz. Girişimler, ilkeler ve Güvenlik Merkezi 'nin önerilerini nasıl birbirleriyle ilgili daha fazla bilgi edinmek için bkz. [güvenlik ilkeleri, girişimler ve öneriler nelerdir?](security-policy-concept.md).
