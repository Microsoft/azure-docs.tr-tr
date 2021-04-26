---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 1a12e0e772d9fb575a720e3f6ceacbfe6331b71e
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107867080"
---
|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[İzin verilen tanımlar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe56962a6-4747-49cd-b67b-bf8b01975c4c) |Bu ilke, kuruluşunuzun kaynakları dağıtırken belirleyebileceği konumları kısıtlamanıza olanak verir. Coğrafi uyumluluk gereksinimlerinizi zorunlu kılmak için kullanabilirsiniz. Kaynak gruplarını, Microsoft. AzureActiveDirectory/b2cDirectories ve ' Global ' bölgesini kullanan kaynakları dışlar. |reddedebilir |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/AllowedLocations_Deny.json) |
|[Kaynak grupları için izin verilen konumlar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe765b5de-1225-4ba3-bd56-1ac6695af988) |Bu ilke, kuruluşunuzun içinde kaynak grupları oluşturabileceğiniz konumları kısıtlamanıza olanak sağlar. Coğrafi uyumluluk gereksinimlerinizi zorunlu kılmak için kullanabilirsiniz. |reddedebilir |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/ResourceGroupAllowedLocations_Deny.json) |
|[İzin verilen kaynak türleri](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa08ec900-254a-4555-9bf5-e42af04b5c5c) |Bu ilke, kuruluşunuzun dağıtabileceğiniz kaynak türlerini belirtmenizi sağlar. Bu ilkeden yalnızca ' Tags ' ve ' Location ' desteği olan kaynak türleri etkilenir. Tüm kaynakları kısıtlamak için lütfen bu ilkeyi çoğaltın ve ' Mode ' öğesini ' All ' olarak değiştirin. |reddedebilir |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/AllowedResourceTypes_Deny.json) |
|[Kaynak konumu denetim kaynak grubu konumuyla eşleşiyor](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a914e76-4921-4c19-b460-a2d36003525a) |Kaynak konumunun kaynak grubu konumuyla eşleşip eşleşmediğini denetle |denetlenmesini |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/ResourcesInResourceGroupLocation_Audit.json) |
|[Özel RBAC kurallarının kullanımını denetleme](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa451c1ef-c6ca-483d-87ed-f49761e3ffb5) |Hataya açık olan özel RBAC rolleri yerine ' Owner, katılımcısı, Reader ' gibi yerleşik rolleri denetleyin. Özel rolleri kullanmak özel durum olarak değerlendirilir ve kapsamlı bir inceleme ve tehdit modelleme gerektirir |Denetim, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |
|[Özel abonelik sahibi rolleri mevcut olmamalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F10ee2ea2-fb4d-45b8-a7e9-a2e770044cd9) |Bu ilke, özel abonelik sahibi rolü olmamasını sağlar. |Denetim, devre dışı |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json) |
|[İzin verilmeyen kaynak türleri](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6c112d4e-5bc7-47ae-a041-ea2d9dccd749) |Ortamınızda dağıtılabilecek kaynak türlerini kısıtlayın. Kaynak türlerini sınırlandırma, ortamınızın karmaşıklık ve saldırı yüzeyini azaltabilir, ayrıca maliyetleri yönetmeye de yardımcı olur. Uyumluluk sonuçları yalnızca uyumlu olmayan kaynaklar için gösterilir. |Denetim, reddetme, devre dışı |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/InvalidResourceTypes_Deny.json) |
