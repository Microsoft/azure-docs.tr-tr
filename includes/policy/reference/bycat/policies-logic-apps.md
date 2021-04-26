---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: b70057229a2d1fe47ca5e27f9ee4f1338521ff46
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107867127"
---
|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Logic Apps Tümleştirme Hizmeti Ortamı, müşteri tarafından yönetilen anahtarlarla şifrelenmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1fafeaf6-7927-4059-a50a-8eb2a7a6f2b5) |Müşteri tarafından yönetilen anahtarları kullanarak Logic Apps verileri geri kalanında şifrelemeyi yönetmek için Tümleştirme Hizmeti Ortamı içine dağıtın. Varsayılan olarak, müşteri verileri hizmet tarafından yönetilen anahtarlarla şifrelenir, ancak yasal uyumluluk standartlarını karşılamak için müşteri tarafından yönetilen anahtarlar yaygın olarak gereklidir. Müşteri tarafından yönetilen anahtarlar, verilerin oluşturulmuş ve sizin tarafınızdan sahip olduğu bir Azure Key Vault anahtarla şifrelenmesini sağlar. Anahtar yaşam döngüsü için, döndürme ve yönetim de dahil olmak üzere tam denetim ve sorumluluğa sahipsiniz. |Denetim, reddetme, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Logic%20Apps/LogicApps_ISEWithCustomerManagedKey_AuditDeny.json) |
|[Logic Apps Tümleştirme Hizmeti Ortamı dağıtılmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdc595cb1-1cde-45f6-8faf-f88874e1c0e1) |Bir sanal ağda Logic Apps Tümleştirme Hizmeti Ortamı dağıtmak gelişmiş Logic Apps ağ ve güvenlik özelliklerinin kilidini açar ve ağ yapılandırmanız üzerinde daha fazla denetim sağlar. Daha fazla bilgi: [https://aka.ms/integration-service-environment](https://aka.ms/integration-service-environment) . Tümleştirme Hizmeti Ortamı içine dağıtmak, şifreleme anahtarlarınızı yönetmenize olanak tanıyarak gelişmiş veri koruması sağlayan müşteri tarafından yönetilen anahtarlarla şifrelemeye de olanak tanır. Bu, genellikle uyumluluk gereksinimlerini karşılamak için kullanılır. |Denetim, reddetme, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Logic%20Apps/LogicApps_LogicAppsInISE_AuditDeny.json) |
|[Logic Apps kaynak günlüklerinin etkinleştirilmesi gerekir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34f95f76-5386-4de7-b824-0d8478470c9d) |Kaynak günlüklerinin etkinleştirilmesi için denetim. Bu, araştırma amacıyla kullanılacak etkinlik izlerini yeniden oluşturmayı sağlar; bir güvenlik olayı gerçekleştiğinde veya ağınız tehlikede olduğunda |Auditınotexists, devre dışı |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Logic%20Apps/LogicApps_AuditDiagnosticLog_Audit.json) |
