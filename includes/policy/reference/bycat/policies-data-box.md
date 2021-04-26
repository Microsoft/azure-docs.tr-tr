---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 7839d6be9eab20253c1518a550bcb2b6ebd3dcc4
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107867056"
---
|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Azure Data Box işler cihazdaki bekleyen veriler için çift şifrelemeyi etkinleştirmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc349d81b-9985-44ae-a8da-ff98d108ede8) |Cihazdaki bekleyen veriler için yazılım tabanlı ikinci bir şifreleme katmanını etkinleştirin. Cihaz, bekleyen veriler için Gelişmiş Şifreleme Standardı 256 bit şifreleme aracılığıyla zaten korunuyor. Bu seçenek, ikinci bir veri şifrelemesi katmanı ekler. |Denetim, reddetme, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Box/DataBox_DoubleEncryption_Audit.json) |
|[Azure Data Box işlerin, cihaz kilidi açma parolasını şifrelemek için müşteri tarafından yönetilen bir anahtar kullanması gerekir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86efb160-8de7-451d-bc08-5d475b0aadae) |Azure Data Box cihaz kilit açma parolasının şifrelenmesini denetlemek için müşteri tarafından yönetilen bir anahtar kullanın. Müşteri tarafından yönetilen anahtarlar Ayrıca, cihazı hazırlamak ve verileri otomatik olarak kopyalamak için Data Box hizmeti tarafından cihazın kilit açma parolasının yönetilmesine de yardımcı olur. Cihazdaki veriler, Gelişmiş Şifreleme Standardı 256 bit Şifrelemeli geri kalan bilgisayarlarda zaten şifrelenmiştir ve cihaz kilidi açma parolası, Microsoft tarafından yönetilen anahtarla varsayılan olarak şifrelenir. |Denetim, reddetme, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Box/DataBox_CMK_Audit.json) |
