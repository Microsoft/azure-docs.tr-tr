---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 0bbd833b840809828cb13cf807a8c150d42283d8
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107866956"
---
|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Azure Active Directory Domain Services yönetilen etki alanları yalnızca TLS 1,2 modunu kullanmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3aa87b5a-7813-4b57-8a43-42dd9df5aaa7) |Yönetilen etki alanlarınız için yalnızca TLS 1,2 modunu kullanın. Varsayılan olarak, Azure AD Domain Services NTLM v1 ve TLS v1 gibi şifrelemelerin kullanımını mümkün değildir. Bu şifrelemeler bazı eski uygulamalar için gerekli olabilir, ancak zayıf kabul edilir ve gerek duymuyorsanız devre dışı bırakılabilir. Yalnızca TLS 1,2 modu etkin olduğunda, TLS 1,2 kullanmayan bir istek yapan tüm istemciler başarısız olur. Daha fazla bilgi edinin [https://docs.microsoft.com/azure/active-directory-domain-services/secure-your-domain](https://docs.microsoft.com/azure/active-directory-domain-services/secure-your-domain) . |Denetim, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Active%20Directory/AADDomainServices_TLS_Audit.json) |
