---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 28de9d80a1e5568b70f48e789233af38388fbe70
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107874287"
---
|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[FHıR için Azure API 'SI, bekleyen verileri şifrelemek için müşteri tarafından yönetilen bir anahtar kullanmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F051cba44-2429-45b9-9649-46cec11c7119) |Bu bir düzenleme veya uyum gereksinimidir FHıR için Azure API 'sinde depolanan verilerin geri kalanında şifrelemeyi denetlemek için müşteri tarafından yönetilen bir anahtar kullanın. Müşteri tarafından yönetilen anahtarlar Ayrıca, hizmet tarafından yönetilen anahtarlarla varsayılan olarak en üstüne bir şifreleme katmanı ekleyerek Çift şifreleme sağlar. |Denetim, devre dışı |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_EnableByok_Audit.json) |
|[FHıR için Azure API 'SI özel bağlantı kullanmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1ee56206-5dd1-42ab-b02d-8aae8b1634ce) |FHIR için Azure API 'SI, en az bir onaylanan özel uç nokta bağlantısına sahip olmalıdır. Bir sanal ağdaki istemciler özel bağlantı noktası bağlantılarına sahip kaynaklara güvenli şekilde erişebilir. Daha fazla bilgi için şu adresi ziyaret edin: [https://aka.ms/fhir-privatelink](https://aka.ms/fhir-privatelink) . |Denetim, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_PrivateLink_Audit.json) |
|[CORS, her etki alanının FHıR için API 'nize erişmesine izin vermemelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fea8f8a-4169-495d-8307-30ec335f387d) |Çıkış noktaları arası kaynak paylaşımı (CORS), tüm etki alanlarının FHıR için API 'nize erişmesine izin vermemelidir. API 'nizi FHıR 'ye karşı korumak için tüm etki alanları için erişimi kaldırın ve bağlanmasına izin verilen etki alanlarını açık olarak tanımlayın. |Denetim, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_RestrictCORSAccess_Audit.json) |
