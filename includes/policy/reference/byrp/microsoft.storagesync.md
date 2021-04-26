---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 871a648eada2e710604d5fb42ea83a8559049b6a
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107876030"
---
|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Azure Dosya Eşitleme özel bağlantı kullanmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1d320205-c6a1-4ac6-873d-46224024e8e2) |Belirtilen depolama eşitleme hizmeti kaynağı için özel bir uç nokta oluşturulması, depolama eşitleme hizmeti kaynağınızı, internet erişimli genel uç nokta yerine kuruluşunuzun ağının özel IP adresi alanından adresetmenize olanak tanır. Özel bir uç noktanın kendi başına oluşturulması, genel uç noktayı devre dışı bırakmaz. |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageSync_PrivateEndpoint_AuditIfNotExists.json) |
|[Özel uç noktalarla Azure Dosya Eşitleme yapılandırma](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb35dddd9-daf7-423b-8375-5a5b86806d5a) |Belirtilen depolama eşitleme hizmeti kaynağı için özel bir uç nokta dağıtılır. Bu, depolama eşitleme hizmeti kaynağınızın, internet erişimli genel uç nokta yerine, kuruluşunuzun ağının özel IP adresi alanından adreslemenize olanak sağlar. Tek başına bir veya daha fazla özel bitiş noktasının varlığı, genel uç noktasını devre dışı bırakmaz. |DeployIfNotExists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageSync_PrivateEndpoint_DeployIfNotExists.json) |
|[Değiştir-ortak ağ erişimini devre dışı bırakmak için Azure Dosya Eşitleme yapılandırın](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e07b2e9-6cd9-4c40-9ccb-52817b95133b) |Azure Dosya Eşitleme internet erişimli ortak uç noktası, kuruluş ilkeniz tarafından devre dışı bırakılır. Depolama Eşitleme hizmetine hala özel uç noktaları aracılığıyla erişebilirsiniz. |Değiştirme, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageSync_IncomingTrafficPolicy_Modify.json) |
|[Azure Dosya Eşitleme için genel ağ erişimi devre dışı bırakılmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F21a8cd35-125e-4d13-b82d-2e19b7208bb7) |Genel uç noktasını devre dışı bırakmak, kuruluşunuzun ağındaki onaylanan özel uç noktalara giden isteklerle depolama eşitleme hizmeti kaynağınızın erişimini kısıtlamanıza olanak sağlar. Genel uç noktaya yönelik isteklere izin verme konusunda doğal olarak güvenli olmayan hiçbir şey yoktur, ancak yasal, yasal veya kurumsal ilke gereksinimlerini karşılamak için devre dışı bırakmak isteyebilirsiniz. Kaynağın incomingTrafficPolicy değerini AllowVirtualNetworksOnly olarak ayarlayarak bir depolama eşitleme hizmeti için genel uç noktasını devre dışı bırakabilirsiniz. |Denetim, reddetme, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageSync_IncomingTrafficPolicy_AuditDeny.json) |
