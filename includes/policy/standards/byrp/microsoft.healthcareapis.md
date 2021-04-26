---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 72fb4efc7a30ebaa3299f4c479bc02ad4b6f5386
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107862155"
---
## <a name="cmmc-level-3"></a>CMMC düzey 3

Tüm Azure hizmetleri için kullanılabilen Azure Ilkesi 'nin Bu uyumluluk standardına nasıl eşlendiğini gözden geçirmek için bkz. [Azure Ilke mevzuatı uyumluluğu-CMMC düzey 3](../../../../articles/governance/policy/samples/cmmc-l3.md).
Bu uyumluluk standardı hakkında daha fazla bilgi için bkz. [Sisecurity vade modeli sertifikası (CMMC)](https://www.acq.osd.mil/cmmc/docs/CMMC_Model_Main_20200203.pdf).

|Etki alanı |Denetim KIMLIĞI |Denetim başlığı |İlke<br /><sub>(Azure portal)</sub> |İlke sürümü<br /><sub>GitHub</sub>  |
|---|---|---|---|---|
|Erişim Denetimi |AC. 1.001 |Bilgi sistemi erişimini yetkili kullanıcılara, yetkili kullanıcı adına işlem gören işlemlere ve cihazlara (diğer bilgi sistemleri dahil) sınırlayın. |[CORS, her etki alanının FHıR için API 'nize erişmesine izin vermemelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fea8f8a-4169-495d-8307-30ec335f387d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_RestrictCORSAccess_Audit.json) |
|Erişim Denetimi |AC. 1.002 |Bilgi sistemi erişimini, yetkili kullanıcıların yürütmesine izin verilen işlem ve işlev türleriyle sınırlayın. |[CORS, her etki alanının FHıR için API 'nize erişmesine izin vermemelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fea8f8a-4169-495d-8307-30ec335f387d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_RestrictCORSAccess_Audit.json) |
|Erişim Denetimi |AC. 2.016 |, Onaylanan Yetkilendirmelere uygun olarak CUı akışını kontrol edin. |[CORS, her etki alanının FHıR için API 'nize erişmesine izin vermemelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fea8f8a-4169-495d-8307-30ec335f387d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_RestrictCORSAccess_Audit.json) |
|Yapılandırma Yönetimi |CM. 3.068 |Gerekli olmayan programlar, işlevler, bağlantı noktaları, protokoller ve hizmetlerin kullanımını kısıtlayın, devre dışı bırakın veya engelleyin. |[CORS, her etki alanının FHıR için API 'nize erişmesine izin vermemelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fea8f8a-4169-495d-8307-30ec335f387d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_RestrictCORSAccess_Audit.json) |
|Sistem ve Iletişim koruması |SC. 3.177 |CUı 'nin gizliliğini korumak için kullanıldığında FIPS tarafından doğrulanan şifrelemeyi kullanmayı sağlar. |[FHıR için Azure API 'SI, bekleyen verileri şifrelemek için müşteri tarafından yönetilen bir anahtar kullanmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F051cba44-2429-45b9-9649-46cec11c7119) |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_EnableByok_Audit.json) |
|Sistem ve Iletişim koruması |SC. 3.183 |Ağ iletişimi trafiğini varsayılan olarak reddedin ve özel duruma göre ağ iletişim trafiğine izin verin (yani, Tümünü Reddet, özel duruma izin ver). |[CORS, her etki alanının FHıR için API 'nize erişmesine izin vermemelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fea8f8a-4169-495d-8307-30ec335f387d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_RestrictCORSAccess_Audit.json) |

