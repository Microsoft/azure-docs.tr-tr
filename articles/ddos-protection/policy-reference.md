---
title: Azure DDoS koruma standardı için yerleşik ilke tanımları
description: Azure DDoS koruma standardı için Azure Ilkesi yerleşik ilke tanımlarını listeler. Bu yerleşik ilke tanımları, Azure kaynaklarınızı yönetmek için yaygın yaklaşımlar sağlar.
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/21/2021
ms.author: yitoh
ms.custom: subject-policy-reference
ms.topic: include
ms.openlocfilehash: b58dddef0aa63aec525e039f333a7a785c32d23e
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107875656"
---
# <a name="azure-policy-built-in-definitions-for-azure-ddos-protection-standard"></a>Azure DDoS koruma standardı için Azure Ilkesi yerleşik tanımları

Bu sayfa, Azure DDoS koruma standardı için [Azure ilkesi](../governance/policy/overview.md) yerleşik ilke tanımlarının bir dizinidir. Diğer hizmetlere yönelik diğer Azure Ilkesi için yerleşik olarak [bulunan Azure ilkesi yerleşik tanımları](../governance/policy/samples/built-in-policies.md)bölümüne bakın.

Her yerleşik ilke tanımının adı Azure portal ilke tanımına bağlanır. Kaynağı [Azure Policy GitHub](https://github.com/Azure/azure-policy)deposunda görüntülemek için **Sürüm** sütunundaki bağlantıyı kullanın.

## <a name="azure-ddos-protection-standard"></a>Azure DDoS Koruması Standart

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Sanal ağlar Azure DDoS koruma standardı tarafından korunmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F94de2ad3-e0c1-4caf-ad78-5d47bbc83d3d)|Azure DDoS koruma standardı ile sanal ağlarınızı Volumetric ve protokol saldırılarına karşı koruyun. Daha fazla bilgi için, adresini ziyaret edin [https://aka.ms/ddosprotectiondocs](https://aka.ms/ddosprotectiondocs) .|Değiştirme, denetim, devre dışı|[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkDdosStandard_Audit.json)|
|[Genel IP adreslerinde Azure DDoS koruma standardı için kaynak günlükleri etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F752154a7-1e0f-45c6-a880-ac75a7e4f648)|Tanılama ayarlarındaki genel IP adresleri için kaynak günlüklerini bir Log Analytics çalışma alanına akışa yönelik olarak etkinleştirin. Bildirimler, raporlar ve akış günlükleri aracılığıyla DDoS saldırılarını azaltmak için gerçekleştirilen saldırı trafiğine ve eylemlerine yönelik ayrıntılı görünürlük alın.|Auditınotexists, DeployIfNotExists, devre dışı|[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/PublicIpDdosLogging_Audit.json)|


## <a name="next-steps"></a>Sonraki adımlar

- Yerleşik ilkeleri görmek için [Azure İlkesi GitHub deposuna](https://github.com/Azure/azure-policy) gidin.
- [Azure İlkesi tanımı yapısını](../governance/policy/concepts/definition-structure.md) gözden geçirin.
- [İlkenin etkilerini anlama](../governance/policy/concepts/effects.md) konusunu gözden geçirin.
