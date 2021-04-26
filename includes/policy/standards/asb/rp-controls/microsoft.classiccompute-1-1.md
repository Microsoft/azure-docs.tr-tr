---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 0bde67a3082ce7b188179bbe78dc2e09a6a32f17
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107881205"
---
|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Internet 'e yönelik sanal makineler ağ güvenlik gruplarıyla korunmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |Ağ güvenlik grupları (NSG) ile erişimi kısıtlayarak sanal makinelerinizi olası tehditlere karşı koruyun. NSG 'ler ile trafiği denetleme hakkında daha fazla bilgi edinin [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[Sanal makinenizde IP Iletimi devre dışı bırakılmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbd352bd5-2853-4985-bf0d-73806b4a5744) |Bir sanal makinenin NIC 'inde IP iletmeyi etkinleştirmek, makinenin diğer hedeflere adreslenen trafiği almasına izin verir. IP iletimi nadiren gereklidir (örneğin, VM 'yi bir ağ sanal gereci olarak kullanırken) ve bu nedenle ağ güvenlik ekibi tarafından incelenmelidir. |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_IPForwardingOnVirtualMachines_Audit.json) |
|[Yönetim bağlantı noktaları sanal makinelerinizde kapatılmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22730e10-96f6-4aac-ad84-9383d35b5917) |Açık Uzaktan yönetim bağlantı noktaları, sanal makinenizin Internet tabanlı saldırılardan yüksek düzeyde riske sunulmasını sağlar. Bu saldırılar, makineye yönetici erişimi kazanmak için zorlamalı kimlik bilgilerini yanılmaya çalışır. |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OpenManagementPortsOnVirtualMachines_Audit.json) |
