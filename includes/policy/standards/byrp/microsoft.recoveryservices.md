---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 1e6de5fbca19fb8509c2bc84b53daa8618011b99
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107878010"
---
## <a name="azure-security-benchmark"></a>Azure Güvenlik Karşılaştırması

[Azure Güvenlik kıyaslaması](/security/benchmark/azure/introduction) , bulut Çözümlerinizi Azure 'da nasıl güvence altına almak için öneriler sağlar. Bu hizmetin Azure Güvenlik kıyaslaması ile tamamen nasıl eşlendiğini görmek için bkz. [Azure Güvenlik kıyaslaması eşleme dosyaları](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

Tüm Azure hizmetleri için kullanılabilen Azure Ilkesi 'nin Bu uyumluluk standardına nasıl eşlendiğini gözden geçirmek için bkz. [Azure Ilke mevzuatı uyumluluğu-Azure Güvenlik kıyaslaması](../../../../articles/governance/policy/samples/azure-security-benchmark.md).

|Etki alanı |Denetim KIMLIĞI |Denetim başlığı |İlke<br /><sub>(Azure portal)</sub> |İlke sürümü<br /><sub>GitHub</sub>  |
|---|---|---|---|---|
|Yedekleme ve Kurtarma |BR-1 |Düzenli otomatik yedeklemeler sağlayın |[Sanal makineler için Azure Backup etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |
|Yedekleme ve Kurtarma |BR-2 |Yedekleme verilerini şifreleme |[Sanal makineler için Azure Backup etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |

## <a name="azure-security-benchmark-v1"></a>Azure Güvenlik kıyaslaması v1

[Azure Güvenlik kıyaslaması](/security/benchmark/azure/introduction) , bulut Çözümlerinizi Azure 'da nasıl güvence altına almak için öneriler sağlar. Bu hizmetin Azure Güvenlik kıyaslaması ile tamamen nasıl eşlendiğini görmek için bkz. [Azure Güvenlik kıyaslaması eşleme dosyaları](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

Tüm Azure hizmetleri için kullanılabilen Azure Ilkesi 'nin Bu uyumluluk standardına nasıl eşlendiğini gözden geçirmek için bkz. [Azure Ilke mevzuatı uyumluluğu-Azure Güvenlik kıyaslaması](../../../../articles/governance/policy/samples/azure-security-benchmark.md).

|Etki alanı |Denetim KIMLIĞI |Denetim başlığı |İlke<br /><sub>(Azure portal)</sub> |İlke sürümü<br /><sub>GitHub</sub>  |
|---|---|---|---|---|
|Veri Kurtarma |9,1 |Düzenli Otomatik yedeklemeli UPS sağlayın |[Sanal makineler için Azure Backup etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |
|Veri Kurtarma |9.2 |Tüm sistem yedeklemelerini gerçekleştirin ve müşterinin yönettiği tüm anahtarları yedekleyin |[Sanal makineler için Azure Backup etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |

## <a name="cmmc-level-3"></a>CMMC düzey 3

Tüm Azure hizmetleri için kullanılabilen Azure Ilkesi 'nin Bu uyumluluk standardına nasıl eşlendiğini gözden geçirmek için bkz. [Azure Ilke mevzuatı uyumluluğu-CMMC düzey 3](../../../../articles/governance/policy/samples/cmmc-l3.md).
Bu uyumluluk standardı hakkında daha fazla bilgi için bkz. [Sisecurity vade modeli sertifikası (CMMC)](https://www.acq.osd.mil/cmmc/docs/CMMC_Model_Main_20200203.pdf).

|Etki alanı |Denetim KIMLIĞI |Denetim başlığı |İlke<br /><sub>(Azure portal)</sub> |İlke sürümü<br /><sub>GitHub</sub>  |
|---|---|---|---|---|
|Kurtarma |RE. 2.137 |Düzenli olarak veri yedekleme işlemleri gerçekleştirin ve test edin. |[Sanal makineler için Azure Backup etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |
|Kurtarma |RE. 3.139 |Kuruluş tanımlı olarak eksiksiz, kapsamlı ve dayanıklı veri yedeklemeleri düzenli olarak gerçekleştirin. |[Sanal makineler için Azure Backup etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |

## <a name="hipaa-hitrust-92"></a>HIPAA HITRUST 9.2

Tüm Azure hizmetleri için kullanılabilen Azure Ilkesinin Bu uyumluluk standardına nasıl eşlendiğini gözden geçirmek için bkz. [Azure Ilke mevzuatı uyumluluğu-HIPAA HITRUST 9,2](../../../../articles/governance/policy/samples/hipaa-hitrust-9-2.md).
Bu uyumluluk standardı hakkında daha fazla bilgi için bkz. [HIPAA HITRUST 9,2](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html).

|Etki alanı |Denetim KIMLIĞI |Denetim başlığı |İlke<br /><sub>(Azure portal)</sub> |İlke sürümü<br /><sub>GitHub</sub>  |
|---|---|---|---|---|
|Yedekleme |1620.09 l1Organizational. 8-09. l |Yedekleme hizmeti üçüncü taraf tarafından teslim edildiğinde, hizmet düzeyi sözleşmesi, yedekleme bilgilerinin gizliliğini, bütünlüğünü ve kullanılabilirliğini denetlemek için ayrıntılı korumalar içerir. |[Sanal makineler için Azure Backup etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |
|Yedekleme |1625.09 l3Organizational. 34-09. l |Üç (3) yedek nesil (tam ve tüm ilgili artımlı veya fark yedeklemeleri) yerinde depolanır ve hem yerinde hem de site dışı yedeklemeler ad, tarih, saat ve eylem ile günlüğe kaydedilir. |[Sanal makineler için Azure Backup etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |
|Yedekleme |1699.09 l1Organizational. 10-09. l |Veri yedekleme işlemindeki iş gücü üyeleri rolleri ve sorumlulukları, iş gücüne göre tanımlanır ve iletilir; Özellikle, kendi cihazını getir (KCG) kullanıcılarının, cihazlarındaki kuruluş ve/veya istemci verilerinin yedeklerini gerçekleştirmesi gerekir. |[Sanal makineler için Azure Backup etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |

