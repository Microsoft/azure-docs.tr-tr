---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: b4fa21e37d8a0794898cee827c73559162eed76d
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107867950"
---
|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Log Analytics aracısının beklendiği gibi bağlı olmadığı Windows makinelerini denetle](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6265018c-d7e2-432f-a75d-094d5f6f4465) |Önkoşulların ilke atama kapsamına dağıtılmasını gerektirir. Ayrıntılar için, adresini ziyaret edin [https://aka.ms/gcpol](https://aka.ms/gcpol) . Aracı yüklü değilse veya yüklüyse ancak AgentConfigManager. MgmtSvcCfg COM nesnesi, ilke parametresinde belirtilen KIMLIğIN dışında bir çalışma alanına kayıtlı olduğunu döndürürse, makineler uyumlu değildir. |Auditınotexists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsLogAnalyticsAgentConnection_AINE.json) |
|[Log Analytics Aracısı sanal makine ölçek kümelerine yüklenmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fefbde977-ba53-4479-b8e9-10b957924fbf) |Log Analytics Aracısı yüklenmemişse, bu ilke tüm Windows/Linux sanal makine ölçek kümelerini denetler. |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/VMSS_LogAnalyticsAgent_AuditIfNotExists.json) |
|[Log Analytics Aracısı sanal makinelere yüklenmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa70ca396-0a34-413a-88e1-b956c1e683be) |Log Analytics Aracısı yüklenmemişse, bu ilke tüm Windows/Linux sanal makinelerini denetler. |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/VirtualMachines_LogAnalyticsAgent_AuditIfNotExists.json) |
