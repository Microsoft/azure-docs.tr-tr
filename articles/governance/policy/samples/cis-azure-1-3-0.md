---
title: CIS için yasal uyumluluk ayrıntıları Microsoft Azure temelleri kıyaslama 1.3.0
description: CIS 'nin ayrıntıları, 1.3.0 mevzuatı uyumluluğu yerleşik girişiminin Microsoft Azure temelleri. Her denetim, değerlendirmede yardımcı olan bir veya daha fazla Azure Ilke tanımına eşlenir.
ms.date: 04/21/2021
ms.topic: sample
ms.custom: generated
ms.openlocfilehash: 14a93281eb93bee1bf6ecd15da8811088a692c29
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107865630"
---
# <a name="details-of-the-cis-microsoft-azure-foundations-benchmark-130-regulatory-compliance-built-in-initiative"></a>CIS Microsoft Azure temelleri Kıyaslandırma ayrıntıları 1.3.0 mevzuat uyumluluğu yerleşik girişimi

Aşağıdaki makalede, Azure Ilke mevzuatı uyumluluğu yerleşik girişim tanımının, CIS 'deki **Uyumluluk etki alanları** ve **denetimleriyle** Microsoft Azure, temelleri kıyaslama 1.3.0 ' ye nasıl eşleştiği açıklanır.
Bu uyumluluk standardı hakkında daha fazla bilgi için bkz. [cıs Microsoft Azure temelleri kıyaslaması 1.3.0](https://www.cisecurity.org/benchmark/azure/). _Sahipliği_ anlamak için bkz. [Azure ilke ilkesi tanımı](../concepts/definition-structure.md#type) ve [bulutta paylaşılan sorumluluk](../../../security/fundamentals/shared-responsibility.md).

Aşağıdaki eşlemeler, **Microsoft Azure, temelleri kıyaslama 1.3.0** denetimlerini sağlar. Doğrudan belirli bir **Uyumluluk etki alanına** geçmek için sağ taraftaki gezintiyi kullanın. Denetimlerin birçoğu bir [Azure Policy](../overview.md) Initiative tanımıyla uygulanır. Tüm girişim tanımını gözden geçirmek için Azure portal **ilkeyi** açın ve **tanımlar** sayfasını seçin.
Ardından, **Yönetim kıyaslandırma v 1.3.0** mevzuatı uyumluluğu yerleşik girişim tanımını Microsoft Azure, CIS 'leri bulun ve seçin.

> [!IMPORTANT]
> Aşağıdaki her denetim bir veya daha fazla [Azure ilke](../overview.md) tanımı ile ilişkilidir.
> Bu ilkeler, denetimiyle [uyumluluğu değerlendirmenize](../how-to/get-compliance-data.md) yardımcı olabilir; Ancak, bir denetim ile bir veya daha fazla ilke arasında genellikle bire bir veya tam eşleşme yoktur. Bu nedenle, Azure Ilkesiyle **uyumlu** yalnızca ilke tanımlarının kendilerine başvurur; Bu, bir denetimin tüm gereksinimleriyle tamamen uyumlu olduğunuzdan emin değildir. Buna ek olarak, uyumluluk standardı şu anda herhangi bir Azure Ilke tanımı tarafından açıklanmayan denetimler içerir. Bu nedenle, Azure Ilkesinde uyumluluk, genel uyumluluk durumunuzu yalnızca kısmi görünümüdür. Bu uyumluluk standardı için uyumluluk etki alanları, denetimler ve Azure Ilke tanımları arasındaki ilişkilendirmeler zaman içinde değişebilir. Değişiklik geçmişini görüntülemek için [GitHub kayıt geçmişine](https://github.com/Azure/azure-policy/commits/master/built-in-policies/policySetDefinitions/Regulatory%20Compliance/CISv1_3_0.json)bakın.

## <a name="identity-and-access-management"></a>Kimlik ve Erişim Yönetimi

### <a name="ensure-that-multi-factor-authentication-is-enabled-for-all-privileged-users"></a>Multi-Factor Authentication 'ın tüm ayrıcalıklı kullanıcılar için etkinleştirildiğinden emin olun

**Kimlik**: cıs Azure 1,1 **sahipliği**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Aboneliğinizde yazma izinleri olan hesaplarda MFA etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |Hesapların veya kaynakların ihlal oluşmasını engellemek için yazma ayrıcalıklarına sahip tüm abonelik hesapları için Multi-Factor Authentication (MFA) etkinleştirilmelidir. |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |
|[MFA, aboneliğinizde sahip izinleri olan hesaplarda etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |Multi-Factor Authentication (MFA), hesapların veya kaynakların ihlaline engel olmak için sahip izinleri olan tüm abonelik hesapları için etkinleştirilmelidir. |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |

### <a name="ensure-that-multi-factor-authentication-is-enabled-for-all-non-privileged-users"></a>Multi-Factor Authentication 'ın tüm ayrıcalıklı olmayan kullanıcılar için etkinleştirildiğinden emin olun

**Kimlik**: cıs Azure 1,2 **sahipliği**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[MFA, aboneliğinizde okuma izinleri olan hesaplarda etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |Hesapların veya kaynakların ihlal oluşmasını engellemek için okuma ayrıcalıklarına sahip tüm abonelik hesapları için Multi-Factor Authentication (MFA) etkinleştirilmelidir. |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |

### <a name="ensure-guest-users-are-reviewed-on-a-monthly-basis"></a>Konuk kullanıcıların aylık olarak incelendiğinden emin olun

**Kimlik**: cıs Azure 1,3 **sahipliği**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Sahip izinleri olan dış hesaplar aboneliğinizden kaldırılmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8456c1c-aa66-4dfb-861a-25d127b775c9) |İzlenmeyen erişimi engellemek için sahip izinlerine sahip dış hesapların aboneliğinizden kaldırılması gerekir. |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsWithOwnerPermissions_Audit.json) |
|[Okuma izinlerine sahip dış hesapların aboneliğinizden kaldırılması gerekir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5f76cf89-fbf2-47fd-a3f4-b891fa780b60) |İzlenmeyen erişimi engellemek için, okuma ayrıcalıklarına sahip dış hesapların aboneliğinizden kaldırılması gerekir. |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsReadPermissions_Audit.json) |
|[Yazma izinlerine sahip dış hesapların aboneliğinizden kaldırılması gerekir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5c607a2e-c700-4744-8254-d77e7c9eb5e4) |İzlenmeyen erişimi engellemek için, yazma ayrıcalıklarına sahip dış hesapların aboneliğinizden kaldırılması gerekir. |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsWritePermissions_Audit.json) |

### <a name="ensure-that-no-custom-subscription-owner-roles-are-created"></a>Özel abonelik sahibi rollerinin oluşturulmadığından emin olun

**Kimlik**: cıs Azure 1,21 **sahipliği**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Özel abonelik sahibi rolleri mevcut olmamalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F10ee2ea2-fb4d-45b8-a7e9-a2e770044cd9) |Bu ilke, özel abonelik sahibi rolü olmamasını sağlar. |Denetim, devre dışı |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json) |

## <a name="security-center"></a>Güvenlik Merkezi

### <a name="ensure-that-azure-defender-is-set-to-on-for-servers"></a>Azure Defender 'ın sunucular için açık olarak ayarlandığından emin olun

**Kimlik**: cıs Azure 2,1 **sahipliği**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Sunucular için Azure Defender etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4da35fc9-c9e7-4960-aec9-797fe7d9051d) |Sunucular için Azure Defender, sunucu iş yükleri için gerçek zamanlı tehdit koruması sağlar ve şüpheli etkinliklerle ilgili uyarıların yanı sıra sağlamlaştırma önerilerini de oluşturur. |Auditınotexists, devre dışı |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnVM_Audit.json) |

### <a name="ensure-that-azure-defender-is-set-to-on-for-app-service"></a>App Service için Azure Defender 'ın açık olarak ayarlandığından emin olun

**Kimlik**: cıs Azure 2,2 **sahipliği**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[App Service için Azure Defender etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2913021d-f2fd-4f3d-b958-22354e2bdbcb) |App Service için Azure Defender, bulut ölçeğinden yararlanır ve Azure 'un ortak Web uygulaması saldırılarını izlemek için bir bulut sağlayıcısı olarak sahip olduğu görünürlüğe sahiptir. |Auditınotexists, devre dışı |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnAppServices_Audit.json) |

### <a name="ensure-that-azure-defender-is-set-to-on-for-azure-sql-database-servers"></a>Azure Defender 'ın Azure SQL veritabanı sunucuları için açık olarak ayarlandığından emin olun

**Kimlik**: cıs Azure 2,3 **sahipliği**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Azure SQL veritabanı sunucuları için Azure Defender etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7fe3b40f-802b-4cdd-8bd4-fd799c948cc2) |SQL için Azure Defender, ortaya çıkabilecek ve olası veritabanı güvenlik açıklarını azaltmaya yönelik işlevsellik sunar, SQL veritabanlarına tehditler oluşturabilecek anormal etkinlikleri algılayarak hassas verileri bulabilir ve sınıflandırın. |Auditınotexists, devre dışı |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedDataSecurityOnSqlServers_Audit.json) |

### <a name="ensure-that-azure-defender-is-set-to-on-for-sql-servers-on-machines"></a>Makinelerdeki SQL Server 'lar için Azure Defender 'ın açık olarak ayarlandığından emin olun

**Kimlik**: cıs Azure 2,4 **sahipliği**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Makinelerdeki SQL Server 'lar için Azure Defender etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6581d072-105e-4418-827f-bd446d56421b) |SQL için Azure Defender, ortaya çıkabilecek ve olası veritabanı güvenlik açıklarını azaltmaya yönelik işlevsellik sunar, SQL veritabanlarına tehditler oluşturabilecek anormal etkinlikleri algılayarak hassas verileri bulabilir ve sınıflandırın. |Auditınotexists, devre dışı |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedDataSecurityOnSqlServerVirtualMachines_Audit.json) |

### <a name="ensure-that-azure-defender-is-set-to-on-for-storage"></a>Azure Defender 'ın depolama için açık olarak ayarlandığından emin olun

**Kimlik**: cıs Azure 2,5 **sahipliği**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Depolama için Azure Defender etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F308fbb08-4ab8-4e67-9b29-592e93fb94fa) |Depolama için Azure Defender, depolama hesaplarına erişmek veya açıktan yararlanmak için olağandışı ve potansiyel olarak zararlı denemelere yönelik algılamalar sağlar |Auditınotexists, devre dışı |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnStorageAccounts_Audit.json) |

### <a name="ensure-that-azure-defender-is-set-to-on-for-kubernetes"></a>Azure Defender 'ın Kubernetes için açık olarak ayarlandığından emin olun

**Kimlik**: cıs Azure 2,6 **sahipliği**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Kubernetes için Azure Defender etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F523b5cd1-3e23-492f-a539-13118b6d1e3a) |Kubernetes için Azure Defender, Kapsayıcılı ortamlar için gerçek zamanlı tehdit koruması sağlar ve şüpheli etkinlikler için uyarı oluşturur. |Auditınotexists, devre dışı |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnKubernetesService_Audit.json) |

### <a name="ensure-that-azure-defender-is-set-to-on-for-container-registries"></a>Azure Defender 'ın kapsayıcı kayıt defterleri için açık olarak ayarlandığından emin olun

**Kimlik**: cıs Azure 2,7 **sahipliği**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Kapsayıcı kayıt defterleri için Azure Defender etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc25d9a16-bc35-4e15-a7e5-9db606bf9ed4) |Kapsayıcı kayıt defterleri için Azure Defender, son 30 gün içinde çekilen, Kayıt defterinize gönderilen veya içeri aktarılan tüm görüntüleri taramak ve görüntü başına ayrıntılı bulguları ortaya çıkaran bir güvenlik açığı taraması sağlar. |Auditınotexists, devre dışı |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnContainerRegistry_Audit.json) |

### <a name="ensure-that-azure-defender-is-set-to-on-for-key-vault"></a>Key Vault için Azure Defender 'ın açık olarak ayarlandığından emin olun

**Kimlik**: cıs Azure 2,8 **sahipliği**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Key Vault için Azure Defender etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e6763cc-5078-4e64-889d-ff4d9a839047) |Key Vault için Azure Defender, önemli kasa hesaplarına erişme veya açıktan yararlanma amacıyla olağandışı ve potansiyel olarak zararlı girişimleri algılayarak ek bir koruma ve güvenlik zekası katmanı sağlar. |Auditınotexists, devre dışı |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnKeyVaults_Audit.json) |

### <a name="ensure-that-automatic-provisioning-of-monitoring-agent-is-set-to-on"></a>' İzleme aracısının otomatik sağlanması ' öğesinin ' on ' olarak ayarlandığından emin olun

**Kimlik**: cıs Azure 2,11 **sahipliği**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Log Analytics aracısının otomatik olarak sağlanması aboneliğinizde etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F475aae12-b88a-4572-8b36-9b712b2b3a17) |Azure Güvenlik Merkezi, güvenlik açıklarını ve tehditleri izlemek için Azure sanal makinelerinizden veri toplar. Veriler, makineden güvenlikle ilgili çeşitli yapılandırma ve olay günlüklerini okuyan ve verileri analiz için Log Analytics çalışma alanınıza kopyalayan, daha önce Microsoft Monitoring Agent (MMA) olarak bilinen Log Analytics Aracısı tarafından toplanır. Otomatik sağlamanın, aracıyı desteklenen tüm Azure VM 'lerine ve oluşturulan tüm yeni makinelere otomatik olarak dağıtması için etkinleştirmenizi öneririz. |Auditınotexists, devre dışı |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Automatic_provisioning_log_analytics_monitoring_agent.json) |

### <a name="ensure-additional-email-addresses-is-configured-with-a-security-contact-email"></a>' Ek e-posta adreslerinin ' bir güvenlik iletişim e-postası ile yapılandırıldığından emin olun

**Kimlik**: cıs Azure 2,13 **sahipliği**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Abonelikler güvenlik sorunları için bir iletişim e-posta adresine sahip olmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f4f78b8-e367-4b10-a341-d9a4ad5cf1c7) |Aboneliklerinizden birinde potansiyel bir güvenlik ihlali olduğunda kuruluşunuzdaki ilgili kişilerin bilgilendirilmesine emin olmak için Güvenlik Merkezi 'nden e-posta bildirimleri alacak bir güvenlik kişisi ayarlayın. |Auditınotexists, devre dışı |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Security_contact_email.json) |

### <a name="ensure-that-notify-about-alerts-with-the-following-severity-is-set-to-high"></a>' Aşağıdaki önem derecesine sahip uyarıları bildir ' ' High ' olarak ayarlandığından emin olun

**Kimlik**: cıs Azure 2,14 **sahipliği**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Yüksek önem derecesine sahip uyarılar için e-posta bildirimi etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6e2593d9-add6-4083-9c9b-4b7d2188c899) |Aboneliklerinizden birinde potansiyel bir güvenlik ihlali olduğunda kuruluşunuzdaki ilgili kişilerin bildirilmesini sağlamak için, güvenlik merkezi 'nde yüksek önem derecesine sahip uyarılar için e-posta bildirimlerini etkinleştirin. |Auditınotexists, devre dışı |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Email_notification.json) |

## <a name="storage-accounts"></a>Depolama Hesapları

### <a name="ensure-that-secure-transfer-required-is-set-to-enabled"></a>' Güvenli aktarım gerekli ' ' Enabled ' olarak ayarlandığından emin olun

**Kimlik**: cıs Azure 3,1 **sahipliği**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Depolama hesaplarına güvenli aktarım etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |Depolama hesabınızda güvenli aktarım gereksinimini denetleyin. Güvenli aktarım, depolama hesabınızı yalnızca güvenli bağlantılardan (HTTPS) istekleri kabul edecek şekilde zorlayan bir seçenektir. HTTPS kullanımı, sunucu ile hizmet arasında kimlik doğrulaması sağlar ve geçiş sırasında ortadaki adam, gizlice dinleme ve oturum ele geçirme gibi ağ katmanı saldırılarına karşı verileri korur |Denetim, reddetme, devre dışı |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |

### <a name="ensure-that-public-access-level-is-set-to-private-for-blob-containers"></a>' Genel erişim düzeyi ' ' nin blob kapsayıcıları için özel olarak ayarlandığından emin olun

**Kimlik**: cıs Azure 3,5 **sahipliği**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Depolama hesabı genel erişimine izin verilmemelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4fa4b6c0-31ca-4c0d-b10d-24b96f62a751) |Azure Storage 'da kapsayıcılara ve BLOB 'lara anonim genel okuma erişimi, verileri paylaşmanın kolay bir yoludur ancak güvenlik riskleri sunabilir. İstenmeyen anonim erişimden kaynaklanan veri ihlallerinin oluşmasını önlemek için, Microsoft, senaryolarınız için gerekli olmadığı takdirde bir depolama hesabına genel erişimi engellemeyi önerir. |Denetim, reddetme, devre dışı |[2.0.1-Önizleme](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/ASC_Storage_DisallowPublicBlobAccess_Audit.json) |

### <a name="ensure-default-network-access-rule-for-storage-accounts-is-set-to-deny"></a>Depolama hesapları için varsayılan ağ erişim kuralının Reddet olarak ayarlandığından emin olun

**Kimlik**: cıs Azure 3,6 **sahipliği**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Depolama hesapları, ağ erişimini kısıtlıyor olmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34c877ad-507e-4c82-993e-3452a6e0ad3c) |Depolama hesaplarına ağ erişimi kısıtlanmalıdır. Ağ kurallarını, yalnızca izin verilen ağların uygulamalarının depolama hesabına erişebilmesi için yapılandırın. Belirli internet veya şirket içi istemcilerden gelen bağlantılara izin vermek için, belirli Azure sanal ağlarından veya genel İnternet IP adresi aralıklarına yönelik trafiğe erişim verilebilir |Denetim, reddetme, devre dışı |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |
|[Depolama hesapları, sanal ağ kurallarını kullanarak ağ erişimini kısıtlamalı](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2a1a9cdf-e04d-429a-8416-3bfb72a1b26f) |Sanal ağ kurallarını kullanarak, IP tabanlı filtreleme yerine tercih edilen bir yöntem olarak depolama hesaplarınızı olası tehditlere karşı koruyun. IP tabanlı filtrelemenin devre dışı bırakılması, genel IP 'Lerin depolama hesaplarınıza erişmesini önler. |Denetim, reddetme, devre dışı |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageAccountOnlyVnetRulesEnabled_Audit.json) |

### <a name="ensure-trusted-microsoft-services-is-enabled-for-storage-account-access"></a>Depolama hesabı erişimi için ' güvenilen Microsoft Hizmetleri 'nin etkinleştirildiğinden emin olun

**Kimlik**: cıs Azure 3,7 **sahipliği**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Depolama hesapları güvenilen Microsoft hizmetlerinden erişime izin verilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc9d007d0-c057-4772-b18c-01e546713bcd) |Depolama hesaplarıyla etkileşime geçen bazı Microsoft Hizmetleri, ağ kuralları aracılığıyla erişim izni verilebilen ağlardan çalışır. Bu tür bir hizmetin istendiği şekilde çalışmasını sağlamak için, güvenilir Microsoft hizmetlerinin ağ kurallarını atlamasına izin verin. Bu hizmetler daha sonra depolama hesabına erişmek için güçlü kimlik doğrulaması kullanacaktır. |Denetim, reddetme, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageAccess_TrustedMicrosoftServices_Audit.json) |

### <a name="ensure-storage-for-critical-data-are-encrypted-with-customer-managed-key"></a>Kritik verilerin depolanması, müşteri tarafından yönetilen anahtarla şifrelendiğinden emin olun

**Kimlik**: cıs Azure 3,9 **sahipliği**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Depolama hesapları, şifreleme için müşteri tarafından yönetilen anahtarı kullanmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6fac406b-40ca-413b-bf8e-0bf964659c25) |Müşteri tarafından yönetilen anahtarları kullanarak depolama hesabınızın güvenliğini daha fazla esneklik sağlar. Müşteri tarafından yönetilen bir anahtar belirttiğinizde verilerinizi şifrelemek ve verilerinize erişimi denetlemek için bu anahtar kullanılır. Müşteri tarafından yönetilen anahtarların kullanılması, anahtar şifreleme anahtarının dönüşünü denetlemek veya şifreli olarak verileri silmek için ek yetenekler sağlar. |Denetim, devre dışı |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageAccountCustomerManagedKeyEnabled_Audit.json) |

## <a name="database-services"></a>Veritabanı Hizmetleri

### <a name="ensure-that-auditing-is-set-to-on"></a>' Denetim ' in ' on ' olarak ayarlandığından emin olun

**Kimlik**: CIS Azure 4.1.1 **sahipliği**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[SQL Server üzerinde denetim etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa6fb4358-5bf4-4ad7-ba82-2cd2f41ce5e9) |SQL Server denetim, sunucudaki tüm veritabanları genelinde veritabanı etkinliklerini izlemek ve bir denetim günlüğüne kaydetmek için etkinleştirilmelidir. |Auditınotexists, devre dışı |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditing_Audit.json) |

### <a name="ensure-that-data-encryption-is-set-to-on-on-a-sql-database"></a>' Veri şifrelemesi 'nin bir SQL veritabanında ' on ' olarak ayarlandığından emin olun

**Kimlik**: CIS Azure 4.1.2 **sahipliği**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[SQL veritabanlarındaki Saydam Veri Şifrelemesi etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17k78e20-9358-41c9-923c-fb736d382a12) |Rest verilerini korumak ve uyumluluk gereksinimlerini karşılamak için saydam veri şifrelemesi etkinleştirilmelidir |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlDBEncryption_Audit.json) |

### <a name="ensure-that-auditing-retention-is-greater-than-90-days"></a>' Denetim ' saklama ' ın 90 günden daha fazla olduğundan emin olun

**Kimlik**: CIS Azure 4.1.3 **sahipliği**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Depolama hesabı hedefine denetim içeren SQL Server 'lar 90 gün bekletme veya daha yüksek ile yapılandırılmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F89099bee-89e0-4b26-a5f4-165451757743) |Olay araştırma amaçları için, SQL Server ' denetiminin depolama hesabı hedefine en az 90 güne kadar veri bekletmesini ayarlamayı öneririz. Çalıştırdığınız bölgeler için gerekli bekletme kurallarını karşılatığınızdan emin olun. Bu, bazen mevzuat standartlarıyla uyumluluk için gereklidir. |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditingRetentionDays_Audit.json) |

### <a name="ensure-that-advanced-threat-protection-atp-on-a-sql-server-is-set-to-enabled"></a>SQL Server 'da Gelişmiş tehdit koruması 'nın (ATP) ' Enabled ' olarak ayarlandığından emin olun

**Kimlik**: CIS Azure 4.2.1 **sahipliği**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Gelişmiş veri güvenliği SQL yönetilen örneği üzerinde etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |Gelişmiş veri güvenliği olmadan her SQL yönetilen örneğini denetleyin. |Auditınotexists, devre dışı |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[Gelişmiş veri güvenliği SQL sunucularınızda etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |Gelişmiş veri güvenliği olmadan SQL sunucularını denetleme |Auditınotexists, devre dışı |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |

### <a name="ensure-that-vulnerability-assessment-va-is-enabled-on-a-sql-server-by-setting-a-storage-account"></a>Bir depolama hesabı ayarlayarak bir SQL Server 'da güvenlik açığı değerlendirmesi (VA) özelliğinin etkinleştirildiğinden emin olun

**Kimlik**: CIS Azure 4.2.2 **sahipliği**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Güvenlik açığı değerlendirmesi SQL yönetilen örneği üzerinde etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1b7aa243-30e4-4c9e-bca8-d0d3022b634a) |Yinelenen güvenlik açığı değerlendirmesi taramaları etkinleştirilmemiş olan her bir SQL yönetilen örneği denetleyin. Güvenlik açığı değerlendirmesi, olası veritabanı güvenlik açıklarını düzeltmenizi, izleyebilir ve yardımcı olabilir. |Auditınotexists, devre dışı |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnManagedInstance_Audit.json) |
|[Güvenlik açığı değerlendirmesi SQL sunucularınızda etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef2a8f2a-b3d9-49cd-a8a8-9a3aaaf647d9) |Yinelenen güvenlik açığı değerlendirmesi taramaları etkinleştirilmemiş olan Azure SQL Server 'ı denetleyin. Güvenlik açığı değerlendirmesi, olası veritabanı güvenlik açıklarını düzeltmenizi, izleyebilir ve yardımcı olabilir. |Auditınotexists, devre dışı |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnServer_Audit.json) |

### <a name="ensure-that-va-setting-send-scan-reports-to-is-configured-for-a-sql-server"></a>SQL Server için tarama raporlarının gönderilmesi için yapılandırılmış VA ayarının yapılandırıldığından emin olun

**Kimlik**: CIS Azure 4.2.4 **sahipliği**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[SQL Server için güvenlik açığı değerlendirmesi ayarları, tarama raporlarını alacak bir e-posta adresi içermelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F057d6cfe-9c4f-4a6d-bc60-14420ea1f1a9) |Güvenlik açığı değerlendirme ayarlarındaki ' ' tarama raporları Gönder ' alanı için bir e-posta adresinin sağlandığından emin olun. Bu e-posta adresi, SQL Server 'lar üzerinde düzenli bir tarama çalıştıktan sonra tarama sonucu özetini alır. |Auditınotexists, devre dışı |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_VulnerabilityAssessmentEmails_Audit.json) |

### <a name="ensure-enforce-ssl-connection-is-set-to-enabled-for-postgresql-database-server"></a>PostgreSQL veritabanı sunucusu için ' SSL bağlantısını zorla ' ' ENABLED ' olarak ayarlandığından emin olun

**Kimlik**: CIS Azure 4.3.1 **sahipliği**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[MySQL veritabanı sunucuları için SSL bağlantısını zorla etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |MySQL için Azure veritabanı, MySQL Server için Azure veritabanı sunucunuzu Güvenli Yuva Katmanı (SSL) kullanarak istemci uygulamalarına bağlamayı destekler. Veritabanı sunucunuz ile istemci uygulamalarınız arasında SSL bağlantılarını zorunlu tutma, sunucu ile uygulamanız arasındaki veri akışını şifreleyerek, ortadaki adam saldırılarına karşı korunmaya yardımcı olur. Bu yapılandırma, veritabanı sunucunuza erişmek için SSL 'nin her zaman etkinleştirilmesini zorunlu kılar. |Denetim, devre dışı |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |

### <a name="ensure-enforce-ssl-connection-is-set-to-enabled-for-mysql-database-server"></a>MySQL veritabanı sunucusu için ' SSL bağlantısını zorla ' özelliğinin ' ENABLED ' olarak ayarlandığından emin olun

**Kimlik**: CIS Azure 4.3.2 **sahipliği**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[PostgreSQL veritabanı sunucuları için SSL bağlantısını zorla etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |PostgreSQL için Azure veritabanı, PostgreSQL için Azure veritabanı sunucunuzu Güvenli Yuva Katmanı (SSL) kullanarak istemci uygulamalarına bağlamayı destekler. Veritabanı sunucunuz ile istemci uygulamalarınız arasında SSL bağlantılarını zorunlu tutma, sunucu ile uygulamanız arasındaki veri akışını şifreleyerek, ortadaki adam saldırılarına karşı korunmaya yardımcı olur. Bu yapılandırma, veritabanı sunucunuza erişmek için SSL 'nin her zaman etkinleştirilmesini zorunlu kılar. |Denetim, devre dışı |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |

### <a name="ensure-server-parameter-log_checkpoints-is-set-to-on-for-postgresql-database-server"></a>PostgreSQL veritabanı sunucusu için ' log_checkpoints ' sunucu parametresinin ' ON ' olarak ayarlandığından emin olun

**Kimlik**: CIS Azure 4.3.3 **sahipliği**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[PostgreSQL veritabanı sunucuları için günlük denetim noktaları etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feb6f77b9-bd53-4e35-a23d-7f65d5f0e43d) |Bu ilke, ortamınızda log_checkpoints ayarı etkinleştirilmeden tüm PostgreSQL veritabanlarının denetlenmesini sağlar. |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableLogCheckpoint_Audit.json) |

### <a name="ensure-server-parameter-log_connections-is-set-to-on-for-postgresql-database-server"></a>PostgreSQL veritabanı sunucusu için ' log_connections ' sunucu parametresinin ' ON ' olarak ayarlandığından emin olun

**Kimlik**: CIS Azure 4.3.4 **sahipliği**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[PostgreSQL veritabanı sunucuları için günlük bağlantıları etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feb6f77b9-bd53-4e35-a23d-7f65d5f0e442) |Bu ilke, ortamınızda log_connections ayarı etkinleştirilmeden tüm PostgreSQL veritabanlarının denetlenmesini sağlar. |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableLogConnections_Audit.json) |

### <a name="ensure-server-parameter-log_disconnections-is-set-to-on-for-postgresql-database-server"></a>PostgreSQL veritabanı sunucusu için ' log_disconnections ' sunucu parametresinin ' ON ' olarak ayarlandığından emin olun

**Kimlik**: CIS Azure 4.3.5 **sahipliği**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[PostgreSQL veritabanı sunucuları için bağlantılar günlüğe kaydedilmez.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feb6f77b9-bd53-4e35-a23d-7f65d5f0e446) |Bu ilke, ortamınızda log_disconnections etkinleştirilmeden herhangi bir PostgreSQL veritabanının denetlenmesini sağlar. |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableLogDisconnections_Audit.json) |

### <a name="ensure-server-parameter-connection_throttling-is-set-to-on-for-postgresql-database-server"></a>PostgreSQL veritabanı sunucusu için ' connection_throttling ' sunucu parametresinin ' ON ' olarak ayarlandığından emin olun

**Kimlik**: CIS Azure 4.3.6 **sahipliği**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[PostgreSQL veritabanı sunucuları için bağlantı azaltma etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5345bb39-67dc-4960-a1bf-427e16b9a0bd) |Bu ilke, bağlantı azaltma etkin olmadan ortamınızdaki tüm PostgreSQL veritabanlarını denetlemeye yardımcı olur. Bu ayar, çok sayıda geçersiz parola oturum açma başarısızlığı için IP başına geçici bağlantı azaltmayı mümkün. |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_ConnectionThrottling_Enabled_Audit.json) |

### <a name="ensure-that-azure-active-directory-admin-is-configured"></a>Azure Active Directory yöneticisinin yapılandırıldığından emin olun

**Kimlik**: cıs Azure 4,4 **sahipliği**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[SQL sunucuları için bir Azure Active Directory Yöneticisi sağlanmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1f314764-cb73-4fc9-b863-8eca98ac36e9) |Azure AD kimlik doğrulamasını etkinleştirmek için SQL sunucunuz için bir Azure Active Directory yöneticisinin sağlamasını denetleyin. Azure AD kimlik doğrulaması, veritabanı kullanıcıları ve diğer Microsoft Hizmetleri için Basitleştirilmiş izin yönetimi ve merkezi kimlik yönetimine izin verebilir |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SQL_DB_AuditServerADAdmins_Audit.json) |

### <a name="ensure-sql-servers-tde-protector-is-encrypted-with-customer-managed-key"></a>SQL Server 'ın TDE koruyucunun, müşteri tarafından yönetilen anahtarla şifrelendiğinden emin olun

**Kimlik**: cıs Azure 4,5 **sahipliği**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[SQL yönetilen örnekler, bekleyen verileri şifrelemek için müşteri tarafından yönetilen anahtarları kullanmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F048248b0-55cd-46da-b1ff-39efd52db260) |Kendi anahtarınızla Saydam Veri Şifrelemesi (TDE) uygulamak, TDE koruyucu üzerinde daha fazla saydamlık ve denetim, HSM ile desteklenen bir dış hizmetle daha fazla güvenlik ve görev ayrımı ile ilgili yükseltme sağlar. Bu öneri, ilişkili bir uyumluluk gereksinimi olan kuruluşlar için geçerlidir. |Auditınotexists, devre dışı |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |
|[SQL Server 'lar, bekleyen verileri şifrelemek için müşteri tarafından yönetilen anahtarları kullanmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0d134df8-db83-46fb-ad72-fe0c9428c8dd) |Kendi anahtarınızla Saydam Veri Şifrelemesi (TDE) uygulamak, TDE koruyucusu üzerinde daha fazla saydamlık ve denetim, HSM ile desteklenen bir dış hizmetle daha fazla güvenlik ve görev ayrımı ile ilgili yükseltme sağlar. Bu öneri, ilişkili bir uyumluluk gereksinimi olan kuruluşlar için geçerlidir. |Auditınotexists, devre dışı |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |

## <a name="logging-and-monitoring"></a>Günlüğe Kaydetme ve İzleme

### <a name="ensure-the-storage-container-storing-the-activity-logs-is-not-publicly-accessible"></a>Etkinlik günlüklerini depolayan depolama kapsayıcısının genel olarak erişilebilir olmadığından emin olun

**Kimlik**: CIS Azure 5.1.3 **sahipliği**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Depolama hesabı genel erişimine izin verilmemelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4fa4b6c0-31ca-4c0d-b10d-24b96f62a751) |Azure Storage 'da kapsayıcılara ve BLOB 'lara anonim genel okuma erişimi, verileri paylaşmanın kolay bir yoludur ancak güvenlik riskleri sunabilir. İstenmeyen anonim erişimden kaynaklanan veri ihlallerinin oluşmasını önlemek için, Microsoft, senaryolarınız için gerekli olmadığı takdirde bir depolama hesabına genel erişimi engellemeyi önerir. |Denetim, reddetme, devre dışı |[2.0.1-Önizleme](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/ASC_Storage_DisallowPublicBlobAccess_Audit.json) |

### <a name="ensure-the-storage-account-containing-the-container-with-activity-logs-is-encrypted-with-byok-use-your-own-key"></a>Etkinlik günlükleriyle kapsayıcıyı içeren depolama hesabının BYOK ile şifrelendiğinden emin olun (kendi anahtarınızı kullanın)

**Kimlik**: CIS Azure 5.1.4 **sahipliği**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Etkinlik günlükleri içeren kapsayıcıyı içeren depolama hesabı BYOK ile şifrelenmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffbb99e8e-e444-4da0-9ff1-75c92f5a85b2) |Bu ilke, kapsayıcıyı etkinlik günlükleriyle içeren depolama hesabının BYOK ile şifrelenip şifrelenmediğini denetler. İlke yalnızca depolama hesabı tasarım tarafından etkinlik günlükleri ile aynı abonelikte yer alıyorsa geçerlidir. Rest 'te Azure depolama şifrelemesi hakkında daha fazla bilgi burada bulunabilir [https://aka.ms/azurestoragebyok](https://aka.ms/azurestoragebyok) .  |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_StorageAccountBYOK_Audit.json) |

### <a name="ensure-that-logging-for-azure-keyvault-is-enabled"></a>Azure Anahtar Kasası günlüğü 'nün ' Enabled ' olduğundan emin olun

**Kimlik**: CIS Azure 5.1.5 **sahipliği**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Key Vault kaynak günlüklerinin etkinleştirilmesi gerekir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf820ca0-f99e-4f3e-84fb-66e913812d21) |Kaynak günlüklerinin etkinleştirilmesi için denetim. Bu, bir güvenlik olayı gerçekleştiğinde veya ağınızın güvenliği tehlikeye düştüğünde araştırma amaçlarıyla kullanılacak etkinlik izlerini yeniden oluşturmayı sağlar |Auditınotexists, devre dışı |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_AuditDiagnosticLog_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-policy-assignment"></a>Ilke ataması oluşturmak için etkinlik günlüğü uyarısının mevcut olduğundan emin olun

**Kimlik**: CIS Azure 5.2.1 **sahipliği**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Belirli Ilke işlemleri için bir etkinlik günlüğü uyarısı bulunmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc5447c04-a4d7-4ba8-a263-c9ee321a6858) |Bu ilke, hiçbir etkinlik günlüğü uyarısı yapılandırılmadığı belirli Ilke işlemlerini denetler. |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_PolicyOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-delete-policy-assignment"></a>Ilke atamasını silme için etkinlik günlüğü uyarısının mevcut olduğundan emin olun

**Kimlik**: CIS Azure 5.2.2 **sahipliği**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Belirli Ilke işlemleri için bir etkinlik günlüğü uyarısı bulunmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc5447c04-a4d7-4ba8-a263-c9ee321a6858) |Bu ilke, hiçbir etkinlik günlüğü uyarısı yapılandırılmadığı belirli Ilke işlemlerini denetler. |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_PolicyOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-or-update-network-security-group"></a>Ağ güvenlik grubu oluşturmak veya güncelleştirmek için etkinlik günlüğü uyarısının mevcut olduğundan emin olun

**Kimlik**: CIS Azure 5.2.3 **sahipliği**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Belirli yönetim işlemleri için bir etkinlik günlüğü uyarısı bulunmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Bu ilke, hiçbir etkinlik günlüğü uyarısı yapılandırılmadığı belirli yönetim işlemlerini denetler. |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-delete-network-security-group"></a>Ağ güvenlik grubunu silmek için etkinlik günlüğü uyarısının mevcut olduğundan emin olun

**Kimlik**: CIS Azure 5.2.4 **sahipliği**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Belirli yönetim işlemleri için bir etkinlik günlüğü uyarısı bulunmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Bu ilke, hiçbir etkinlik günlüğü uyarısı yapılandırılmadığı belirli yönetim işlemlerini denetler. |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-or-update-network-security-group-rule"></a>Ağ güvenlik grubu kuralı oluşturmak veya güncelleştirmek için etkinlik günlüğü uyarısının mevcut olduğundan emin olun

**Kimlik**: CIS Azure 5.2.5 **sahipliği**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Belirli yönetim işlemleri için bir etkinlik günlüğü uyarısı bulunmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Bu ilke, hiçbir etkinlik günlüğü uyarısı yapılandırılmadığı belirli yönetim işlemlerini denetler. |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-the-delete-network-security-group-rule"></a>Ağ güvenlik grubu kuralını Sil kuralı için etkinlik günlüğü uyarısının mevcut olduğundan emin olun

**Kimlik**: CIS Azure 5.2.6 **sahipliği**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Belirli yönetim işlemleri için bir etkinlik günlüğü uyarısı bulunmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Bu ilke, hiçbir etkinlik günlüğü uyarısı yapılandırılmadığı belirli yönetim işlemlerini denetler. |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-or-update-security-solution"></a>Güvenlik çözümü oluşturmak veya güncelleştirmek için etkinlik günlüğü uyarısının mevcut olduğundan emin olun

**Kimlik**: CIS Azure 5.2.7 **sahipliği**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Belirli güvenlik işlemleri için bir etkinlik günlüğü uyarısı bulunmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3b980d31-7904-4bb7-8575-5665739a8052) |Bu ilke, hiçbir etkinlik günlüğü uyarısı yapılandırılmadığı belirli güvenlik işlemlerini denetler. |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_SecurityOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-delete-security-solution"></a>Güvenlik çözümünü silmek için etkinlik günlüğü uyarısının mevcut olduğundan emin olun

**Kimlik**: CIS Azure 5.2.8 **sahipliği**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Belirli güvenlik işlemleri için bir etkinlik günlüğü uyarısı bulunmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3b980d31-7904-4bb7-8575-5665739a8052) |Bu ilke, hiçbir etkinlik günlüğü uyarısı yapılandırılmadığı belirli güvenlik işlemlerini denetler. |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_SecurityOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-or-update-or-delete-sql-server-firewall-rule"></a>SQL Server güvenlik duvarı kuralı oluşturmak veya güncelleştirmek ya da silmek için etkinlik günlüğü uyarısının bulunduğundan emin olun

**Kimlik**: CIS Azure 5.2.9 **sahipliği**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Belirli yönetim işlemleri için bir etkinlik günlüğü uyarısı bulunmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Bu ilke, hiçbir etkinlik günlüğü uyarısı yapılandırılmadığı belirli yönetim işlemlerini denetler. |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-diagnostic-logs-are-enabled-for-all-services-which-support-it"></a>Tanılama günlüklerinin, kendisini destekleyen tüm hizmetler için etkinleştirildiğinden emin olun.

**Kimlik**: cıs Azure 5,3 **sahipliği**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Uygulama hizmetlerindeki tanılama günlükleri etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb607c5de-e7d9-4eee-9e5c-83f1bcee4fa0) |Uygulamada tanılama günlüklerinin etkinleştirilmesini denetleme. Bu, bir güvenlik olayı gerçekleşirse veya ağınızın güvenliği tehlikeye girerse araştırma amacıyla etkinlik izlerini yeniden oluşturmayı sağlar |Auditınotexists, devre dışı |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_AuditLoggingMonitoring_Audit.json) |
|[Azure Data Lake Store kaynak günlüklerinin etkinleştirilmesi gerekir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F057ef27e-665e-4328-8ea3-04b3122bd9fb) |Kaynak günlüklerinin etkinleştirilmesi için denetim. Bu, araştırma amacıyla kullanılacak etkinlik izlerini yeniden oluşturmayı sağlar; bir güvenlik olayı gerçekleştiğinde veya ağınız tehlikede olduğunda |Auditınotexists, devre dışı |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeStore_AuditDiagnosticLog_Audit.json) |
|[Azure Stream Analytics kaynak günlüklerinin etkinleştirilmesi gerekir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9be5368-9bf5-4b84-9e0a-7850da98bb46) |Kaynak günlüklerinin etkinleştirilmesi için denetim. Bu, araştırma amacıyla kullanılacak etkinlik izlerini yeniden oluşturmayı sağlar; bir güvenlik olayı gerçekleştiğinde veya ağınız tehlikede olduğunda |Auditınotexists, devre dışı |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Stream%20Analytics/StreamAnalytics_AuditDiagnosticLog_Audit.json) |
|[Batch hesaplarındaki kaynak günlüklerinin etkinleştirilmesi gerekir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F428256e6-1fac-4f48-a757-df34c2b3336d) |Kaynak günlüklerinin etkinleştirilmesi için denetim. Bu, araştırma amacıyla kullanılacak etkinlik izlerini yeniden oluşturmayı sağlar; bir güvenlik olayı gerçekleştiğinde veya ağınız tehlikede olduğunda |Auditınotexists, devre dışı |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Batch/Batch_AuditDiagnosticLog_Audit.json) |
|[Data Lake Analytics kaynak günlüklerinin etkinleştirilmesi gerekir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc95c74d9-38fe-4f0d-af86-0c7d626a315c) |Kaynak günlüklerinin etkinleştirilmesi için denetim. Bu, araştırma amacıyla kullanılacak etkinlik izlerini yeniden oluşturmayı sağlar; bir güvenlik olayı gerçekleştiğinde veya ağınız tehlikede olduğunda |Auditınotexists, devre dışı |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeAnalytics_AuditDiagnosticLog_Audit.json) |
|[Olay Hub 'ındaki kaynak günlükleri etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F83a214f7-d01a-484b-91a9-ed54470c9a6a) |Kaynak günlüklerinin etkinleştirilmesi için denetim. Bu, araştırma amacıyla kullanılacak etkinlik izlerini yeniden oluşturmayı sağlar; bir güvenlik olayı gerçekleştiğinde veya ağınız tehlikede olduğunda |Auditınotexists, devre dışı |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_AuditDiagnosticLog_Audit.json) |
|[IoT Hub kaynak günlüklerinin etkinleştirilmesi gerekir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F383856f8-de7f-44a2-81fc-e5135b5c2aa4) |Kaynak günlüklerinin etkinleştirilmesi için denetim. Bu, araştırma amacıyla kullanılacak etkinlik izlerini yeniden oluşturmayı sağlar; bir güvenlik olayı gerçekleştiğinde veya ağınız tehlikede olduğunda |Auditınotexists, devre dışı |[3.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTHub_AuditDiagnosticLog_Audit.json) |
|[Key Vault kaynak günlüklerinin etkinleştirilmesi gerekir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf820ca0-f99e-4f3e-84fb-66e913812d21) |Kaynak günlüklerinin etkinleştirilmesi için denetim. Bu, bir güvenlik olayı gerçekleştiğinde veya ağınızın güvenliği tehlikeye düştüğünde araştırma amaçlarıyla kullanılacak etkinlik izlerini yeniden oluşturmayı sağlar |Auditınotexists, devre dışı |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_AuditDiagnosticLog_Audit.json) |
|[Logic Apps kaynak günlüklerinin etkinleştirilmesi gerekir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34f95f76-5386-4de7-b824-0d8478470c9d) |Kaynak günlüklerinin etkinleştirilmesi için denetim. Bu, araştırma amacıyla kullanılacak etkinlik izlerini yeniden oluşturmayı sağlar; bir güvenlik olayı gerçekleştiğinde veya ağınız tehlikede olduğunda |Auditınotexists, devre dışı |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Logic%20Apps/LogicApps_AuditDiagnosticLog_Audit.json) |
|[Arama hizmetlerindeki kaynak günlüklerinin etkinleştirilmesi gerekir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4330a05-a843-4bc8-bf9a-cacce50c67f4) |Kaynak günlüklerinin etkinleştirilmesi için denetim. Bu, araştırma amacıyla kullanılacak etkinlik izlerini yeniden oluşturmayı sağlar; bir güvenlik olayı gerçekleştiğinde veya ağınız tehlikede olduğunda |Auditınotexists, devre dışı |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Search/Search_AuditDiagnosticLog_Audit.json) |
|[Service Bus kaynak günlüklerinin etkinleştirilmesi gerekir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8d36e2f-389b-4ee4-898d-21aeb69a0f45) |Kaynak günlüklerinin etkinleştirilmesi için denetim. Bu, araştırma amacıyla kullanılacak etkinlik izlerini yeniden oluşturmayı sağlar; bir güvenlik olayı gerçekleştiğinde veya ağınız tehlikede olduğunda |Auditınotexists, devre dışı |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditDiagnosticLog_Audit.json) |
|[Sanal makine ölçek kümelerindeki kaynak günlükleri etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7c1b1214-f927-48bf-8882-84f0af6588b1) |Bir olay veya bir uzlaşmaya karşı araştırma gerektiğinde etkinlik izinin yeniden oluşturulması için günlüklerin etkinleştirilmesi önerilir. |Auditınotexists, devre dışı |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/ServiceFabric_and_VMSS_AuditVMSSDiagnostics.json) |

## <a name="networking"></a>Ağ

### <a name="ensure-that-rdp-access-is-restricted-from-the-internet"></a>RDP erişiminin internet 'ten kısıtlanmasını sağlayın

**Kimlik**: cıs Azure 6,1 **sahipliği**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Internet 'ten RDP erişimi engellenmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe372f825-a257-4fb8-9175-797a8a8627d6) |Bu ilke, Internet 'ten RDP erişimine izin veren tüm ağ güvenlik kuralları 'nı denetler |Denetim, devre dışı |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkSecurityGroup_RDPAccess_Audit.json) |

### <a name="ensure-that-ssh-access-is-restricted-from-the-internet"></a>SSH erişiminin internet 'ten kısıtlanmasını sağlayın

**Kimlik**: cıs Azure 6,2 **sahipliği**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Internet 'ten SSH erişimi engellenmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c89a2e5-7285-40fe-afe0-ae8654b92fab) |Bu ilke, Internet 'ten SSH erişimine izin veren tüm ağ güvenlik kuralları 'nı denetler |Denetim, devre dışı |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkSecurityGroup_SSHAccess_Audit.json) |

### <a name="ensure-that-network-watcher-is-enabled"></a>Ağ Izleyicisi 'nin ' etkin ' olduğundan emin olun

**Kimlik**: cıs Azure 6,5 **sahipliği**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Ağ Izleyicisi etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6e2945c-0b7b-40f5-9233-7a5323b5cdc6) |Ağ Izleyicisi, Azure 'daki ve Azure 'dan bir ağ senaryosu düzeyinde koşulları izlemenizi ve tanılamanıza olanak tanıyan bölgesel bir hizmettir. Senaryo düzeyi izleme, bir uçtan uca ağ düzeyi görünümündeki sorunları tanılamanıza olanak sağlar. Ağ Izleyicisi ile kullanılabilen ağ tanılama ve görselleştirme araçları, Azure 'da ağınızı anlamanıza, tanılamanıza ve elde etmenize yardımcı olur. |Auditınotexists, devre dışı |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |

## <a name="virtual-machines"></a>Sanal Makineler

### <a name="ensure-virtual-machines-are-utilizing-managed-disks"></a>Sanal makinelerin yönetilen diskleri kullandığından emin olun

**Kimlik**: cıs Azure 7,1 **sahipliği**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Yönetilen diskleri kullanmayan VM 'Leri denetleme](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F06a78e20-9358-41c9-923c-fb736d382a4d) |Bu ilke yönetilen diskleri kullanmayan VM 'Leri denetler |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/VMRequireManagedDisk_Audit.json) |

### <a name="ensure-that-os-and-data-disks-are-encrypted-with-cmk"></a>' OS ve veri ' disklerinin CMK ile şifrelendiğinden emin olun

**Kimlik**: cıs Azure 7,2 **sahipliği**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Disk şifrelemesi sanal makinelere uygulanmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0961003e-5a0a-4549-abde-af6a37f2724d) |Etkin bir disk şifrelemesi olmayan sanal makineler, Azure Güvenlik Merkezi tarafından öneriler olarak izlenir. |Auditınotexists, devre dışı |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnencryptedVMDisks_Audit.json) |

### <a name="ensure-that-unattached-disks-are-encrypted-with-cmk"></a>' Eklenmemiş diskler 'in CMK ile şifrelendiğinden emin olun

**Kimlik**: cıs Azure 7,3 **sahipliği**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Eklenmemiş diskler şifrelenmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c89a2e5-7285-40fe-afe0-ae8654b92fb2) |Bu ilke, şifrelemeden önce eklenmemiş tüm diskleri denetler. |Denetim, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/UnattachedDisk_Encryption_Audit.json) |

### <a name="ensure-that-only-approved-extensions-are-installed"></a>Yalnızca onaylanan uzantıların yüklü olduğundan emin olun

**Kimlik**: cıs Azure 7,4 **sahipliği**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Yalnızca onaylanan VM uzantıları yüklenmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc0e996f8-39cf-4af9-9f45-83fbde810432) |Bu ilke onaylanmamış sanal makine uzantılarını yönetir. |Denetim, reddetme, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/VirtualMachines_ApprovedExtensions_Audit.json) |

### <a name="ensure-that-the-latest-os-patches-for-all-virtual-machines-are-applied"></a>Tüm sanal makineler için en son işletim sistemi düzeltme eklerinin uygulandığından emin olun

**Kimlik**: cıs Azure 7,5 **sahipliği**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Makinelerinize sistem güncelleştirmeleri yüklenmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86b3d65f-7626-441e-b690-81a8b71cff60) |Sunucularınızdaki eksik güvenlik sistemi güncelleştirmeleri, Azure Güvenlik Merkezi tarafından öneriler olarak izlenir |Auditınotexists, devre dışı |[4.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingSystemUpdates_Audit.json) |

### <a name="ensure-that-the-endpoint-protection-for-all-virtual-machines-is-installed"></a>Tüm sanal makineler için Endpoint Protection 'ın yüklü olduğundan emin olun

**Kimlik**: cıs Azure 7,6 **sahipliği**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Azure Güvenlik Merkezi 'nde eksik Endpoint Protection izleme](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf6cd1bd-1635-48cb-bde7-5b15693900b9) |Yüklü bir Endpoint Protection Aracısı olmayan sunucular, Azure Güvenlik Merkezi tarafından öneriler olarak izlenir |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingEndpointProtection_Audit.json) |

## <a name="other-security-considerations"></a>Güvenlik Hakkında Diğer Önemli Noktalar

### <a name="ensure-that-the-expiration-date-is-set-on-all-keys"></a>Süre sonu tarihinin tüm anahtarlarda ayarlandığından emin olun

**Kimlik**: cıs Azure 8,1 **sahipliği**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Key Vault anahtarların bir sona erme tarihi olmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F152b15f7-8e1f-4c1f-ab71-8c010ba5dbc0) |Şifreleme anahtarlarının tanımlı bir sona erme tarihi olmalıdır ve kalıcı olmaz. Geçerli olan anahtarlar, anahtarın güvenliğini tehlikeye amak için daha uzun bir saldırgan sağlar. Şifreleme anahtarlarında süre sonu tarihleri ayarlamak için önerilen bir güvenlik uygulamasıdır. |Denetim, reddetme, devre dışı |[1.0.1-Önizleme](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Keys_ExpirationSet.json) |

### <a name="ensure-that-the-expiration-date-is-set-on-all-secrets"></a>Süre sonu tarihinin tüm gizli dizi üzerinde ayarlandığından emin olun

**Kimlik**: cıs Azure 8,2 **sahipliği**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Key Vault gizli dizileri için bir sona erme tarihi olmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F98728c90-32c7-4049-8429-847dc0f4fe37) |Gizli dizileri tanımlı bir sona erme tarihine sahip olmalı ve kalıcı olmamalıdır. Geçerli olan gizli dizileri, olası bir saldırganın daha fazla zaman aşmasını sağlar. Gizli dizileri üzerinde sona erme tarihlerini ayarlamak için önerilen bir güvenlik uygulamasıdır. |Denetim, reddetme, devre dışı |[1.0.1-Önizleme](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Secrets_ExpirationSet.json) |

### <a name="ensure-the-key-vault-is-recoverable"></a>Anahtar kasasının kurtarılabilir olduğundan emin olun

**Kimlik**: cıs Azure 8,4 **sahipliği**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Anahtar kasaları Temizleme koruması etkin olmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b60c0b2-2dc2-4e1c-b5c9-abbed971de53) |Bir anahtar kasasının kötü bir şekilde silinmesi kalıcı veri kaybına neden olabilir. Kuruluşunuzda kötü amaçlı bir Insider, anahtar kasalarını silebilir ve temizlerse. Temizleme koruması, geçici olarak silinen Anahtar kasaları için zorunlu bir saklama süresi zorlayarak Insider saldırılarına karşı sizi korur. Kuruluş içinde hiç kimse veya Microsoft, geçici silme Bekletme dönemi sırasında anahtar kasalarınızı temizlenebilir. |Denetim, reddetme, devre dışı |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_Recoverable_Audit.json) |

### <a name="enable-role-based-access-control-rbac-within-azure-kubernetes-services"></a>Azure Kubernetes Hizmetleri içinde rol tabanlı erişim denetimi 'ni (RBAC) etkinleştirme

**Kimlik**: cıs Azure 8,5 **sahipliği**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Rol tabanlı Access Control (RBAC), Kubernetes hizmetlerinde kullanılmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |Kullanıcıların gerçekleştirebileceği eylemlere ayrıntılı filtreleme sağlamak için, Kubernetes hizmet kümelerindeki izinleri yönetmek ve ilgili yetkilendirme ilkelerini yapılandırmak üzere Role-Based Access Control (RBAC) kullanın. |Denetim, devre dışı |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |

## <a name="app-service"></a>App Service

### <a name="ensure-app-service-authentication-is-set-on-azure-app-service"></a>Azure App Service üzerinde App Service kimlik doğrulamasının ayarlandığından emin olun

**Kimlik**: cıs Azure 9,1 **sahipliği**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[API uygulamanızda kimlik doğrulaması etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4ebc54a-46e1-481a-bee2-d4411e95d828) |Azure App Service kimlik doğrulaması, anonim HTTP isteklerinin API uygulamasına ulaşmasını engelleyebilen veya API uygulamasına erişmeden önce belirteçlere sahip olanların kimliğini engelleyebilecek bir özelliktir. |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Authentication_ApiApp_Audit.json) |
|[Işlev uygulamanızda kimlik doğrulaması etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc75248c1-ea1d-4a9c-8fc9-29a6aabd5da8) |Azure App Service kimlik doğrulaması, anonim HTTP isteklerinin Işlev uygulamasına ulaşmasını engelleyebilen veya Işlev uygulamasına erişmeden önce belirteçlere sahip oldukları kimlik doğrulamasından geçebilen bir özelliktir |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Authentication_functionapp_Audit.json) |
|[Web uygulamanızda kimlik doğrulaması etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F95bccee9-a7f8-4bec-9ee9-62c3473701fc) |Azure App Service kimlik doğrulaması, anonim HTTP isteklerinin Web uygulamasına ulaşmasını engelleyebilecek veya Web uygulamasına erişmeden önce belirteçlere sahip oldukları kimlik doğrulamasından geçebilen bir özelliktir |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Authentication_WebApp_Audit.json) |

### <a name="ensure-web-app-redirects-all-http-traffic-to-https-in-azure-app-service"></a>Web uygulamasının tüm HTTP trafiğini Azure App Service ' de HTTPS 'ye yönlendirdiğinden emin olun

**Kimlik**: cıs Azure 9,2 **sahipliği**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Web uygulaması yalnızca HTTPS üzerinden erişilebilir olmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |HTTPS kullanımı, sunucu/hizmet kimlik doğrulamasını sağlar ve ağ katmanı gizlice dinleme saldırılarına karşı geçiş sırasında verileri korur. |Denetim, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

### <a name="ensure-web-app-is-using-the-latest-version-of-tls-encryption"></a>Web uygulamasının en son TLS şifreleme sürümünü kullandığından emin olun

**Kimlik**: cıs Azure 9,3 **sahipliği**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[API uygulamanızda en son TLS sürümü kullanılmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |En son TLS sürümüne yükselt |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[İşlev Uygulaması en son TLS sürümü kullanılmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |En son TLS sürümüne yükselt |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[Web uygulamanızda en son TLS sürümü kullanılmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |En son TLS sürümüne yükselt |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |

### <a name="ensure-the-web-app-has-client-certificates-incoming-client-certificates-set-to-on"></a>Web uygulamasında ' Istemci sertifikaları (gelen istemci sertifikaları) ', ' on ' olarak ayarlanmış olduğundan emin olun

**Kimlik**: cıs Azure 9,4 **sahipliği**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[API uygulamasında ' Istemci sertifikaları (gelen istemci sertifikaları) ', ' on ' olarak ayarlanmış olduğundan emin olun](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0c192fe8-9cbb-4516-85b3-0ade8bd03886) |İstemci sertifikaları, uygulamanın gelen istekler için bir sertifika istemesine izin verir. Yalnızca geçerli sertifikaya sahip istemciler uygulamaya erişebiliyor. |Denetim, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_ClientCert.json) |
|[WEB uygulamasının ' Istemci sertifikaları (gelen istemci sertifikaları) ' olarak ' on ' olarak ayarlandığından emin olun](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5bb220d9-2698-4ee4-8404-b9c30c9df609) |İstemci sertifikaları, uygulamanın gelen istekler için bir sertifika istemesine izin verir. Yalnızca geçerli sertifikaya sahip istemciler uygulamaya erişebiliyor. |Denetim, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Webapp_Audit_ClientCert.json) |
|[İşlev uygulamalarında ' Istemci sertifikaları (gelen istemci sertifikaları) ' etkin olmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feaebaea7-8013-4ceb-9d14-7eb32271373c) |İstemci sertifikaları, uygulamanın gelen istekler için bir sertifika istemesine izin verir. Yalnızca geçerli sertifikaları olan istemciler uygulamaya erişebiliyor. |Denetim, devre dışı |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_ClientCert.json) |

### <a name="ensure-that-register-with-azure-active-directory-is-enabled-on-app-service"></a>Azure Active Directory kayıt App Service etkinleştirildiğinden emin olun

**Kimlik**: cıs Azure 9,5 **sahipliği**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Yönetilen kimlik API uygulamanızda kullanılmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4d441f8-f9d9-4a9e-9cef-e82117cb3eef) |Gelişmiş kimlik doğrulama güvenliği için yönetilen kimlik kullanma |Auditınotexists, devre dışı |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_ApiApp_Audit.json) |
|[Yönetilen kimlik İşlev Uygulaması kullanılmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0da106f2-4ca3-48e8-bc85-c638fe6aea8f) |Gelişmiş kimlik doğrulama güvenliği için yönetilen kimlik kullanma |Auditınotexists, devre dışı |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_FunctionApp_Audit.json) |
|[Yönetilen kimliğin Web uygulamanızda kullanılması gerekir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2b9ad585-36bc-4615-b300-fd4435808332) |Gelişmiş kimlik doğrulama güvenliği için yönetilen kimlik kullanma |Auditınotexists, devre dışı |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_WebApp_Audit.json) |

### <a name="ensure-that-php-version-is-the-latest-if-used-to-run-the-web-app"></a>Web uygulamasını çalıştırmak için kullanıldıysa, ' PHP Version ' nin en son sürümü olduğundan emin olun

**Kimlik**: cıs Azure 9,6 **sahipliği**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[API uygulamasının bir parçası olarak kullanılıyorsa ' PHP Version ' öğesinin en son sürümü olduğundan emin olun](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1bc1795e-d44a-4d48-9b3b-6fff0fd5f9ba) |Düzenli aralıklarla, güvenlik kusurlarında veya ek işlevsellik içermesi nedeniyle PHP yazılımları için yeni sürümler yayımlanır. En son sürümün ve/veya yeni işlevleri olan güvenlik düzeltmelerinin avantajlarından yararlanmak için API uygulamaları için en son PHP sürümünü kullanmak önerilir. Şu anda bu ilke yalnızca Linux Web uygulamaları için geçerlidir. |Auditınotexists, devre dışı |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_PHP_Latest.json) |
|[WEB uygulamasının bir parçası olarak kullanılıyorsa, ' PHP Version ' öğesinin en son sürümü olduğundan emin olun](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7261b898-8a84-4db8-9e04-18527132abb3) |Düzenli aralıklarla, güvenlik kusurlarında veya ek işlevsellik içermesi nedeniyle PHP yazılımları için yeni sürümler yayımlanır. Web uygulamaları için en son PHP sürümünü kullanmak, en son sürümün ve/veya yeni işlevsellikleri varsa güvenlik düzeltmelerinin avantajlarından yararlanmak için önerilir. Şu anda bu ilke yalnızca Linux Web uygulamaları için geçerlidir. |Auditınotexists, devre dışı |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Webapp_Audit_PHP_Latest.json) |

### <a name="ensure-that-python-version-is-the-latest-if-used-to-run-the-web-app"></a>Web uygulamasını çalıştırmak için kullanılıyorsa, ' Python sürümü ' nin en son sürümü olduğundan emin olun

**Kimlik**: cıs Azure 9,7 **sahipliği**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[API uygulamasının bir parçası olarak kullanılıyorsa ' Python sürümü 'nin en son bir sürüm olduğundan emin olun](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F74c3584d-afae-46f7-a20a-6f8adba71a16) |Düzenli aralıklarla, güvenlik kusurlarında veya ek işlevsellik içermesi nedeniyle Python yazılımı için yeni sürümler yayımlanır. En son sürümün ve/veya yeni işlevleri olan güvenlik düzeltmelerinin avantajlarından yararlanmak için API uygulamaları için en son Python sürümünü kullanmak önerilir. Şu anda bu ilke yalnızca Linux Web uygulamaları için geçerlidir. |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_python_Latest.json) |
|[Işlev uygulamasının bir parçası olarak kullanılıyorsa ' Python sürümü 'nin en son bir sürüm olduğundan emin olun](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7238174a-fd10-4ef0-817e-fc820a951d73) |Düzenli aralıklarla, güvenlik kusurlarında veya ek işlevsellik içermesi nedeniyle Python yazılımı için yeni sürümler yayımlanır. En son sürümdeki güvenlik düzeltmelerinin ve/veya yeni işlevsellikleri avantajlarından yararlanmak için Işlev uygulamaları için en son Python sürümünü kullanmak önerilir. Şu anda bu ilke yalnızca Linux Web uygulamaları için geçerlidir. |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_python_Latest.json) |
|[Web uygulamasının bir parçası olarak kullanılıyorsa ' Python sürümü 'nin en son bir sürüm olduğundan emin olun](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7008174a-fd10-4ef0-817e-fc820a951d73) |Düzenli aralıklarla, güvenlik kusurlarında veya ek işlevsellik içermesi nedeniyle Python yazılımı için yeni sürümler yayımlanır. Web uygulamaları için en son Python sürümünü kullanmak, en son sürümün ve/veya yeni işlevsellikleri varsa güvenlik düzeltmelerinin avantajlarından yararlanmak için önerilir. Şu anda bu ilke yalnızca Linux Web uygulamaları için geçerlidir. |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_WebApp_Audit_python_Latest.json) |

### <a name="ensure-that-java-version-is-the-latest-if-used-to-run-the-web-app"></a>Web uygulamasını çalıştırmak için kullanıldıysa, ' Java sürümü ' nin en son sürümü olduğundan emin olun

**Kimlik**: cıs Azure 9,8 **sahipliği**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[API uygulamasının bir parçası olarak kullanılıyorsa ' Java sürümü ' nin en son sürümü olduğundan emin olun](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F88999f4c-376a-45c8-bcb3-4058f713cf39) |Güvenlik kusuru veya ek işlevsellik içermesi nedeniyle, düzenli aralıklarla, Java için yeni sürümler yayımlanır. En son sürümün ve/veya yeni işlevleri olan güvenlik düzeltmelerinin avantajlarından yararlanmak için API uygulamaları için en son Python sürümünü kullanmak önerilir. Şu anda bu ilke yalnızca Linux Web uygulamaları için geçerlidir. |Auditınotexists, devre dışı |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_java_Latest.json) |
|[Işlev uygulamasının bir parçası olarak kullanılıyorsa ' Java sürümü ' nin en son sürümü olduğundan emin olun](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9d0b6ea4-93e2-4578-bf2f-6bb17d22b4bc) |Güvenlik kusuru veya ek işlevsellik içermesi nedeniyle, düzenli aralıklarla, Java Yazılımları için daha yeni sürümler yayımlanır. Işlev uygulamaları için en son Java sürümünü kullanmak, en son sürümün ve/veya yeni işlevsellikleri varsa güvenlik düzeltmelerinin avantajlarından yararlanmak için önerilir. Şu anda bu ilke yalnızca Linux Web uygulamaları için geçerlidir. |Auditınotexists, devre dışı |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_java_Latest.json) |
|[Web uygulamasının bir parçası olarak kullanılıyorsa ' Java sürümü 'nin en son sürümü olduğundan emin olun](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F496223c3-ad65-4ecd-878a-bae78737e9ed) |Güvenlik kusuru veya ek işlevsellik içermesi nedeniyle, düzenli aralıklarla, Java Yazılımları için daha yeni sürümler yayımlanır. Web uygulamaları için en son Java sürümünü kullanmak, en son sürümün ve/veya yeni işlevsellikleri varsa güvenlik düzeltmelerinin avantajlarından yararlanmak için önerilir. Şu anda bu ilke yalnızca Linux Web uygulamaları için geçerlidir. |Auditınotexists, devre dışı |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_WebApp_Audit_java_Latest.json) |

### <a name="ensure-that-http-version-is-the-latest-if-used-to-run-the-web-app"></a>Web uygulamasını çalıştırmak için kullanılıyorsa, ' HTTP Version ' ' ın en son sürümü olduğundan emin olun

**Kimlik**: cıs Azure 9,9 **sahipliği**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[API uygulamasını çalıştırmak için kullanılmışsa ' HTTP Version ' nin en son sürümü olduğundan emin olun](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F991310cd-e9f3-47bc-b7b6-f57b557d07db) |Düzenli aralıklarla, güvenlik kusurlarında veya ek işlevsellik dahil olmak üzere HTTP için yeni sürümler yayımlanır. Web uygulamaları için en son HTTP sürümünü kullanarak, herhangi bir ve/veya daha yeni sürümün yeni işlevsellikleri, güvenlik düzeltmelerinin avantajlarından yararlanın. Şu anda bu ilke yalnızca Linux Web uygulamaları için geçerlidir. |Auditınotexists, devre dışı |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_HTTP_Latest.json) |
|[Işlev uygulamasını çalıştırmak için kullanılmışsa ' HTTP Version ' nin en son sürümü olduğundan emin olun](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe2c1c086-2d84-4019-bff3-c44ccd95113c) |Düzenli aralıklarla, güvenlik kusurlarında veya ek işlevsellik dahil olmak üzere HTTP için yeni sürümler yayımlanır. Web uygulamaları için en son HTTP sürümünü kullanarak, herhangi bir ve/veya daha yeni sürümün yeni işlevsellikleri, güvenlik düzeltmelerinin avantajlarından yararlanın. Şu anda bu ilke yalnızca Linux Web uygulamaları için geçerlidir. |Auditınotexists, devre dışı |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_HTTP_Latest.json) |
|[Web uygulamasını çalıştırmak için kullanılıyorsa, ' HTTP Version ' ' ın en son sürümü olduğundan emin olun](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8c122334-9d20-4eb8-89ea-ac9a705b74ae) |Düzenli aralıklarla, güvenlik kusurlarında veya ek işlevsellik dahil olmak üzere HTTP için yeni sürümler yayımlanır. Web uygulamaları için en son HTTP sürümünü kullanarak, herhangi bir ve/veya daha yeni sürümün yeni işlevsellikleri, güvenlik düzeltmelerinin avantajlarından yararlanın. Şu anda bu ilke yalnızca Linux Web uygulamaları için geçerlidir. |Auditınotexists, devre dışı |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_WebApp_Audit_HTTP_Latest.json) |

### <a name="ensure-ftp-deployments-are-disabled"></a>FTP dağıtımlarının devre dışı bırakıldığından emin olun

**Kimlik**: cıs Azure 9,10 **sahipliği**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[FTPS yalnızca API uygulamanızda gerekli olmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9a1b8c48-453a-4044-86c3-d8bfd823e4f5) |Gelişmiş güvenlik için FTPS zorlamayı etkinleştirin |Auditınotexists, devre dışı |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_AuditFTPS_ApiApp_Audit.json) |
|[FTPS yalnızca İşlev Uygulaması gerekli olmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F399b2637-a50f-4f95-96f8-3a145476eb15) |Gelişmiş güvenlik için FTPS zorlamayı etkinleştirin |Auditınotexists, devre dışı |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_AuditFTPS_FunctionApp_Audit.json) |
|[Web uygulamanızda FTPS gerekli olmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4d24b6d4-5e53-4a4f-a7f4-618fa573ee4b) |Gelişmiş güvenlik için FTPS zorlamayı etkinleştirin |Auditınotexists, devre dışı |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_AuditFTPS_WebApp_Audit.json) |

> [!NOTE]
> Belirli Azure Ilke tanımlarının kullanılabilirliği, Azure Kamu ve diğer ulusal bulutlarda farklılık gösterebilir.

## <a name="next-steps"></a>Sonraki adımlar

Azure Ilkesi ile ilgili ek makaleler:

- [Mevzuata uyumluluğuna](../concepts/regulatory-compliance.md) genel bakış.
- [Girişim tanımı yapısına](../concepts/initiative-definition-structure.md)bakın.
- [Azure ilke örneklerinde](./index.md)diğer örnekleri gözden geçirin.
- [İlkenin etkilerini anlama](../concepts/effects.md) konusunu gözden geçirin.
- [Uyumlu olmayan kaynakları nasıl düzelteceğinizi](../how-to/remediate-resources.md)öğrenin.
