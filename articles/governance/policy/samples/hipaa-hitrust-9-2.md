---
title: HIPAA ZRUST 9,2 için mevzuat uyumluluk ayrıntıları
description: HIPAA ZRUST 9,2 mevzuatı uyumluluğu yerleşik girişiminin ayrıntıları. Her denetim, değerlendirmede yardımcı olan bir veya daha fazla Azure Ilke tanımına eşlenir.
ms.date: 04/21/2021
ms.topic: sample
ms.custom: generated
ms.openlocfilehash: b2843e569854947a83b93c0dde5529cc865721e2
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107864388"
---
# <a name="details-of-the-hipaa-hitrust-92-regulatory-compliance-built-in-initiative"></a>HIPAA ZRUST 9,2 mevzuatı uyumluluğu yerleşik girişiminin ayrıntıları

Aşağıdaki makalede, Azure Ilke mevzuatı uyumluluğu yerleşik girişim tanımının, HIPAA ZRUST 9,2 ' deki **Uyumluluk etki alanları** ve **denetimleriyle** nasıl eşleştiği açıklanır.
Bu uyumluluk standardı hakkında daha fazla bilgi için bkz. [HIPAA HITRUST 9,2](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html). _Sahipliği_ anlamak için bkz. [Azure ilke ilkesi tanımı](../concepts/definition-structure.md#type) ve [bulutta paylaşılan sorumluluk](../../../security/fundamentals/shared-responsibility.md).

Aşağıdaki eşlemeler **HIPAA HITRUST 9,2** denetimlerine göre yapılır. Doğrudan belirli bir **Uyumluluk etki alanına** geçmek için sağ taraftaki gezintiyi kullanın. Denetimlerin birçoğu bir [Azure Policy](../overview.md) Initiative tanımıyla uygulanır. Tüm girişim tanımını gözden geçirmek için Azure portal **ilkeyi** açın ve **tanımlar** sayfasını seçin.
Ardından, **HiTRUST/HIPAA** mevzuat uyumluluğu yerleşik girişim tanımını bulun ve seçin.

Bu yerleşik girişim [HIPAA zrust 9,2 şema örneğinin](../../blueprints/samples/hipaa-hitrust-9-2.md)bir parçası olarak dağıtılır.

> [!IMPORTANT]
> Aşağıdaki her denetim bir veya daha fazla [Azure ilke](../overview.md) tanımı ile ilişkilidir.
> Bu ilkeler, denetimiyle [uyumluluğu değerlendirmenize](../how-to/get-compliance-data.md) yardımcı olabilir; Ancak, bir denetim ile bir veya daha fazla ilke arasında genellikle bire bir veya tam eşleşme yoktur. Bu nedenle, Azure Ilkesiyle **uyumlu** yalnızca ilke tanımlarının kendilerine başvurur; Bu, bir denetimin tüm gereksinimleriyle tamamen uyumlu olduğunuzdan emin değildir. Buna ek olarak, uyumluluk standardı şu anda herhangi bir Azure Ilke tanımı tarafından açıklanmayan denetimler içerir. Bu nedenle, Azure Ilkesinde uyumluluk, genel uyumluluk durumunuzu yalnızca kısmi görünümüdür. Bu uyumluluk standardı için uyumluluk etki alanları, denetimler ve Azure Ilke tanımları arasındaki ilişkilendirmeler zaman içinde değişebilir. Değişiklik geçmişini görüntülemek için [GitHub kayıt geçmişine](https://github.com/Azure/azure-policy/commits/master/built-in-policies/policySetDefinitions/Regulatory%20Compliance/HIPAA_HITRUST_audit.json)bakın.

## <a name="privilege-management"></a>Ayrıcalık yönetimi

### <a name="access-to-management-functions-or-administrative-consoles-for-systems-hosting-virtualized-systems-are-restricted-to-personnel-based-upon-the-principle-of-least-privilege-and-supported-through-technical-controls"></a>Sanallaştırılmış sistemleri barındıran sistemler için yönetim işlevlerine veya yönetim konsollarına erişim, en az ayrıcalık ilkesine ve teknik denetimler aracılığıyla desteklenlerine bağlı olarak personelle kısıtlıdır.

**Kimlik**: 11180.01 c3system. 6-01. c **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Sanal makinelerin yönetim bağlantı noktaları, tam zamanında ağ erişim denetimiyle korunmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |Olası ağ tam zamanında (JıT) erişim, Azure Güvenlik Merkezi tarafından öneriler olarak izlenir |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |

### <a name="privileges-are-formally-authorized-and-controlled-allocated-to-users-on-a-need-to-use-and-event-by-event-basis-for-their-functional-role-eg-user-or-administrator-and-documented-for-each-system-productelement"></a>Ayrıcalıklar, kullanıcılara, işlevsel rolleri (ör. Kullanıcı veya yönetici) için kullanımı gereken ve olay temelinde, kullanıcılara ayrılan ve her bir sistem ürünü/öğesi için belgelenen, bir şekilde yetkilendirilir ve denetlenir.

**Kimlik**: 1143.01 c1system. 123-01. c **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Yönetim bağlantı noktaları sanal makinelerinizde kapatılmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22730e10-96f6-4aac-ad84-9383d35b5917) |Açık Uzaktan yönetim bağlantı noktaları, sanal makinenizin Internet tabanlı saldırılardan yüksek düzeyde riske sunulmasını sağlar. Bu saldırılar, makineye yönetici erişimi kazanmak için zorlamalı kimlik bilgilerini yanılmaya çalışır. |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OpenManagementPortsOnVirtualMachines_Audit.json) |

### <a name="the-organization-explicitly-authorizes-access-to-specific-security-relevant-functions-deployed-in-hardware-software-and-firmware-and-security-relevant-information"></a>Kuruluş, belirli güvenlik ilgili işlevlerine (donanım, yazılım ve bellenim 'da dağıtılır) ve güvenlikle ilgili bilgilere açıkça erişim yetkisi verir.

**Kimlik**: 1144.01 c1system. 4-01. c **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Aboneliğiniz için en fazla 3 sahip belirtilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f11b553-d42e-4e3a-89be-32ca364cad4c) |Güvenliği aşılmış bir sahibe göre ihlal olasılığını azaltmak için 3 adede kadar abonelik sahibi belirlemeniz önerilir. |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateLessThanXOwners_Audit.json) |

### <a name="role-based-access-control-is-implemented-and-capable-of-mapping-each-user-to-one-or-more-roles-and-each-role-to-one-or-more-system-functions"></a>Rol tabanlı erişim denetimi uygulanır ve her bir kullanıcıyı bir veya daha fazla rolle ve her bir rolü bir veya daha fazla sistem işlevi ile eşlenebilecek şekilde işleyebilir.

**Kimlik**: 1145.01 c2system. 1-01. c **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Aboneliğinize birden fazla sahip atanmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09024ccc-0c5f-475e-9457-b7c0d9ed487b) |Yönetici erişiminin artıklığını sağlamak için birden fazla abonelik sahibi atamanız önerilir. |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateMoreThanOneOwner_Audit.json) |

### <a name="the-organization-promotes-the-development-and-use-of-programs-that-avoid-the-need-to-run-with-elevated-privileges-and-system-routines-to-avoid-the-need-to-grant-privileges-to-users"></a>Kuruluş, kullanıcılara Ayrıcalık verme gereksinimini ortadan kaldırmak için yükseltilmiş ayrıcalıklarla ve sistem yordamlarının çalışmasına gerek olmadan kaçınmanın geliştirilmesini ve kullanımını yükseltir.

**Kimlik**: 1146.01 c2system. 23-01. c **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Sahip izinleri olan dış hesaplar aboneliğinizden kaldırılmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8456c1c-aa66-4dfb-861a-25d127b775c9) |İzlenmeyen erişimi engellemek için sahip izinlerine sahip dış hesapların aboneliğinizden kaldırılması gerekir. |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsWithOwnerPermissions_Audit.json) |

### <a name="elevated-privileges-are-assigned-to-a-different-user-id-from-those-used-for-normal-business-use-all-users-access-privileged-services-in-a-single-role-and-such-privileged-access-is-minimized"></a>Yükseltilmiş ayrıcalıklar, normal iş kullanımı için kullanılanlardan farklı bir Kullanıcı KIMLIĞINE atanır, tüm kullanıcılar ayrıcalıklı hizmetlere tek bir rolde erişir ve bu tür ayrıcalıklı erişimler en aza indirilir.

**Kimlik**: 1147.01 c2system. 456-01. c **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Sahip izinleri olan kullanım dışı hesaplar aboneliğinizden kaldırılmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Febb62a0c-3560-49e1-89ed-27e074e9f8ad) |Sahip izinleri olan kullanım dışı hesaplar aboneliğinizden kaldırılmalıdır.  Kullanım dışı bırakılan hesaplar, oturum açma işleminden engellenen hesaplardır. |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveDeprecatedAccountsWithOwnerPermissions_Audit.json) |

### <a name="the-organization-restricts-access-to-privileged-functions-and-all-security-relevant-information"></a>Kuruluş ayrıcalıklı işlevlere ve güvenlikle ilgili tüm bilgilere erişimi kısıtlar.

**Kimlik**: 1148.01 c2system. 78-01. c **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Özel RBAC kurallarının kullanımını denetleme](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa451c1ef-c6ca-483d-87ed-f49761e3ffb5) |Hataya açık olan özel RBAC rolleri yerine ' Owner, katılımcısı, Reader ' gibi yerleşik rolleri denetleyin. Özel rolleri kullanmak özel durum olarak değerlendirilir ve kapsamlı bir inceleme ve tehdit modelleme gerektirir |Denetim, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |
|[Windows makineleri ' güvenlik seçenekleri-hesaplar ' için gereksinimleri karşılamalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fee984370-154a-4ee8-9726-19d900e56fc0) |Windows makineleri, Boş parolaların ve Konuk hesap durumunun yerel hesap kullanımını kısıtlamak için ' güvenlik seçenekleri-hesaplar ' kategorisinde belirtilen grup ilkesi ayarlara sahip olmalıdır. Bu ilke, Konuk yapılandırma ön koşullarının ilke atama kapsamına dağıtılmasını gerektirir. Ayrıntılar için, adresini ziyaret edin [https://aka.ms/gcpol](https://aka.ms/gcpol) . |Auditınotexists, devre dışı |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecurityOptionsAccounts_AINE.json) |

### <a name="the-organization-facilitates-information-sharing-by-enabling-authorized-users-to-determine-a-business-partners-access-when-discretion-is-allowed-as-defined-by-the-organization-and-by-employing-manual-processes-or-automated-mechanisms-to-assist-users-in-making-information-sharingcollaboration-decisions"></a>Kuruluş, kuruluş tarafından tanımlanan ve kullanıcılara bilgi paylaşımı/işbirliği kararları verirken el ile süreçler veya otomatik mekanizmalar kullanarak bir iş ortağının erişimini belirlemesine olanak tanıyarak bilgi paylaşımını kolaylaştırır.

**Kimlik**: 1149.01 c2system. 9-01. c **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Rol tabanlı Access Control (RBAC), Kubernetes hizmetlerinde kullanılmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |Kullanıcıların gerçekleştirebileceği eylemlere ayrıntılı filtreleme sağlamak için, Kubernetes hizmet kümelerindeki izinleri yönetmek ve ilgili yetkilendirme ilkelerini yapılandırmak üzere Role-Based Access Control (RBAC) kullanın. |Denetim, devre dışı |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |

### <a name="the-access-control-system-for-the-system-components-storing-processing-or-transmitting-covered-information-is-set-with-a-default-quotdeny-allquot-setting"></a>Kapsanan bilgileri depolayan, işleyen veya ileten sistem bileşenleri için erişim denetimi sistemi, varsayılan &quot; reddetme-All &quot; ayarıyla ayarlanır.

**Kimlik**: 1150.01 c2system. 10-01. c **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Yönetim bağlantı noktaları sanal makinelerinizde kapatılmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22730e10-96f6-4aac-ad84-9383d35b5917) |Açık Uzaktan yönetim bağlantı noktaları, sanal makinenizin Internet tabanlı saldırılardan yüksek düzeyde riske sunulmasını sağlar. Bu saldırılar, makineye yönetici erişimi kazanmak için zorlamalı kimlik bilgilerini yanılmaya çalışır. |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OpenManagementPortsOnVirtualMachines_Audit.json) |

### <a name="the-organization-limits-authorization-to-privileged-accounts-on-information-systems-to-a-pre-defined-subset-of-users"></a>Kuruluş, bilgi sistemlerindeki ayrıcalıklı hesaplar için Yetkilendirmeyi, kullanıcıların önceden tanımlanmış bir alt kümesiyle sınırlandırır.

**Kimlik**: 1151.01 c3system. 1-01. c **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Aboneliğiniz için en fazla 3 sahip belirtilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f11b553-d42e-4e3a-89be-32ca364cad4c) |Güvenliği aşılmış bir sahibe göre ihlal olasılığını azaltmak için 3 adede kadar abonelik sahibi belirlemeniz önerilir. |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateLessThanXOwners_Audit.json) |

### <a name="the-organization-audits-the-execution-of-privileged-functions-on-information-systems-and-ensures-information-systems-prevent-non-privileged-users-from-executing-privileged-functions"></a>Kuruluş, bilgi sistemlerinde ayrıcalıklı işlevlerin yürütülmesini denetler ve bilgi sistemlerinin ayrıcalıksız kullanıcıların ayrıcalıklı işlevler yürütmesini engellemesine olanak sağlar.

**Kimlik**: 1152.01 c3system. 2-01. c **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Aboneliğinize birden fazla sahip atanmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09024ccc-0c5f-475e-9457-b7c0d9ed487b) |Yönetici erişiminin artıklığını sağlamak için birden fazla abonelik sahibi atamanız önerilir. |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateMoreThanOneOwner_Audit.json) |

### <a name="all-file-system-access-not-explicitly-required-is-disabled-and-only-authorized-users-are-permitted-access-to-only-that-which-is-expressly-required-for-the-performance-of-the-users-job-duties"></a>Açık olarak gerekli olmayan tüm dosya sistemi erişimi devre dışıdır ve yalnızca yetkili kullanıcılara yalnızca kullanıcıların iş görevlerini performansı için açıkça gerekli olan erişime izin verilir.

**Kimlik**: 1153.01 c3system. 35-01. c **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Rol tabanlı Access Control (RBAC), Kubernetes hizmetlerinde kullanılmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |Kullanıcıların gerçekleştirebileceği eylemlere ayrıntılı filtreleme sağlamak için, Kubernetes hizmet kümelerindeki izinleri yönetmek ve ilgili yetkilendirme ilkelerini yapılandırmak üzere Role-Based Access Control (RBAC) kullanın. |Denetim, devre dışı |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |

### <a name="contractors-are-provided-with-minimal-system-and-physical-access-only-after-the-organization-assesses-the-contractors-ability-to-comply-with-its-security-requirements-and-the-contractor-agrees-to-comply"></a>Yükleniciler, kuruluşun güvenlik gereksinimlerini karşılayamaz ve yüklenicisi uyumlu kabul ederse en az sistem ve fiziksel erişim ile sağlanır.

**Kimlik**: 1154.01 c3system. 4-01. c **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Aboneliğiniz için en fazla 3 sahip belirtilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f11b553-d42e-4e3a-89be-32ca364cad4c) |Güvenliği aşılmış bir sahibe göre ihlal olasılığını azaltmak için 3 adede kadar abonelik sahibi belirlemeniz önerilir. |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateLessThanXOwners_Audit.json) |

## <a name="user-authentication-for-external-connections"></a>Dış bağlantılar için Kullanıcı kimlik doğrulaması

### <a name="strong-authentication-methods-such-as-multi-factor-radius-or-kerberos-for-privileged-access-and-chap-for-encryption-of-credentials-for-dialup-methods-are-implemented-for-all-external-connections-to-the-organizations-network"></a>Kuruluşlar ağına yönelik tüm dış bağlantılar için çok faktörlü, radius veya Kerberos (ayrıcalıklı erişim için) ve CHAP (çevirmeli yöntemler için kimlik bilgilerini şifreleme) gibi güçlü kimlik doğrulama yöntemleri uygulanır.

**Kimlik**: 1116.01 j1organizational. 145-01. j **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[MFA, aboneliğinizde sahip izinleri olan hesaplarda etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |Multi-Factor Authentication (MFA), hesapların veya kaynakların ihlaline engel olmak için sahip izinleri olan tüm abonelik hesapları için etkinleştirilmelidir. |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |

### <a name="remote-access-by-vendors-and-business-partners-eg-for-remote-maintenance-is-disableddeactivated-when-not-in-use"></a>Satıcılara ve iş ortaklarına göre uzaktan erişim (örneğin, uzaktan bakım için), kullanımda olmadığında devre dışı bırakılır/devre dışı bırakılır.

**Kimlik**: 1117.01 j1organizational. 23-01. j **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Aboneliğinizde yazma izinleri olan hesaplarda MFA etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |Hesapların veya kaynakların ihlal oluşmasını engellemek için yazma ayrıcalıklarına sahip tüm abonelik hesapları için Multi-Factor Authentication (MFA) etkinleştirilmelidir. |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |

### <a name="organizations-implement-encryption-eg-nbspvpn-solutions-or-private-lines-and-logs-remote-access-to-the-organizations-network-by-employees-contractors-or-third-party-eg-vendors"></a>Kuruluşlar, şifreleme (örneğin &nbsp; , VPN çözümleri veya özel satırlar) uygular ve çalışanların, yüklenicilerin veya üçüncü taraflarca (örn. satıcı) uzaktan erişimini kuruluşun ağına kaydeder.

**Kimlik**: 1118.01 j2organizational. 124-01. j **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[MFA, aboneliğinizde okuma izinleri olan hesaplarda etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |Hesapların veya kaynakların ihlal oluşmasını engellemek için okuma ayrıcalıklarına sahip tüm abonelik hesapları için Multi-Factor Authentication (MFA) etkinleştirilmelidir. |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |

### <a name="network-equipment-is-checked-for-unanticipated-dial-up-capabilities"></a>Ağ ekipmanı, beklenmeyen çevirmeli yetenekler için denetlenir.

**Kimlik**: 1119.01 j2organizational. 3-01. j **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Sanal makinelerin yönetim bağlantı noktaları, tam zamanında ağ erişim denetimiyle korunmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |Olası ağ tam zamanında (JıT) erişim, Azure Güvenlik Merkezi tarafından öneriler olarak izlenir |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |

### <a name="remote-administration-sessions-are-authorized-encrypted-and-employ-increased-security-measures"></a>Uzaktan yönetim oturumları yetkilendirildi, şifrelenir ve artırılmış güvenlik önlemleri ister.

**Kimlik**: 1121.01 j3organizational. 2-01. j **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[MFA, aboneliğinizde sahip izinleri olan hesaplarda etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |Multi-Factor Authentication (MFA), hesapların veya kaynakların ihlaline engel olmak için sahip izinleri olan tüm abonelik hesapları için etkinleştirilmelidir. |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |

### <a name="if-encryption-is-not-used-for-dial-up-connections-the-cio-or-hisher-designated-representative-provides-specific-written-authorization"></a>Şifreleme, çevirmeli bağlantılar için kullanılmazsa, CIO veya belirtilen temsilcisi belirli bir yazılı yetkilendirme sağlar.

**Kimlik**: 1173.01 j1organizational. 6-01. j **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Aboneliğinizde yazma izinleri olan hesaplarda MFA etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |Hesapların veya kaynakların ihlal oluşmasını engellemek için yazma ayrıcalıklarına sahip tüm abonelik hesapları için Multi-Factor Authentication (MFA) etkinleştirilmelidir. |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |

### <a name="the-organization-protects-wireless-access-to-systems-containing-sensitive-information-by-authenticating-both-users-and-devices"></a>Kuruluş, kullanıcıların ve cihazların kimliğini kimlik doğrulaması yaparak hassas bilgiler içeren sistemlere kablosuz erişimi korur.

**Kimlik**: 1174.01 j1organizational. 7-01. j **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[MFA, aboneliğinizde okuma izinleri olan hesaplarda etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |Hesapların veya kaynakların ihlal oluşmasını engellemek için okuma ayrıcalıklarına sahip tüm abonelik hesapları için Multi-Factor Authentication (MFA) etkinleştirilmelidir. |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |

### <a name="remote-access-to-business-information-across-public-networks-only-takes-place-after-successful-identification-and-authentication"></a>Ortak ağlarda iş bilgilerine uzaktan erişim, yalnızca başarılı kimlik ve kimlik doğrulamasından sonra gerçekleşir.

**Kimlik**: 1175.01 j1organizational. 8-01. j **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Sanal makinelerin yönetim bağlantı noktaları, tam zamanında ağ erişim denetimiyle korunmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |Olası ağ tam zamanında (JıT) erişim, Azure Güvenlik Merkezi tarafından öneriler olarak izlenir |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |

### <a name="the-organization-requires-a-callback-capability-with-re-authentication-to-verify-dial-up-connections-from-authorized-locations"></a>Kuruluş, yetkili konumlardan çevirmeli bağlantıları doğrulamak için yeniden kimlik doğrulama özelliğine sahip bir geri çağırma özelliği gerektirir.

**Kimlik**: 1176.01 j2organizational. 5-01. j **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[MFA, aboneliğinizde sahip izinleri olan hesaplarda etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |Multi-Factor Authentication (MFA), hesapların veya kaynakların ihlaline engel olmak için sahip izinleri olan tüm abonelik hesapları için etkinleştirilmelidir. |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |

### <a name="user-ids-assigned-to-vendors-are-reviewed-in-accordance-with-the-organizations-access-review-policy-at-a-minimum-annually"></a>Satıcılara atanan kullanıcı kimlikleri, en az bir yıllık olarak kuruluşun erişim gözden geçirme ilkesine göre gözden geçirilir.

**Kimlik**: 1177.01 j2organizational. 6-01. j **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Aboneliğinizde yazma izinleri olan hesaplarda MFA etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |Hesapların veya kaynakların ihlal oluşmasını engellemek için yazma ayrıcalıklarına sahip tüm abonelik hesapları için Multi-Factor Authentication (MFA) etkinleştirilmelidir. |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |

### <a name="node-authentication-including-cryptographic-techniques-eg-machine-certificates-serves-as-an-alternative-means-of-authenticating-groups-of-remote-users-where-they-are-connected-to-a-secure-shared-computer-facility"></a>Şifreleme teknikleri dahil olmak üzere düğüm kimlik doğrulaması (örneğin, makine sertifikaları), güvenli, paylaşılan bir bilgisayar tesisine bağlı oldukları uzak kullanıcı gruplarının kimliğini doğrulamak için alternatif bir yol görevi görür.

**Kimlik**: 1178.01 j2organizational. 7-01. j **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[MFA, aboneliğinizde okuma izinleri olan hesaplarda etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |Hesapların veya kaynakların ihlal oluşmasını engellemek için okuma ayrıcalıklarına sahip tüm abonelik hesapları için Multi-Factor Authentication (MFA) etkinleştirilmelidir. |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |

### <a name="the-information-system-monitors-and-controls-remote-access-methods"></a>Bilgi sistemi uzaktan erişim yöntemlerini izler ve denetler.

**Kimlik**: 1179.01 j3organizational. 1-01. j **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Sanal makinelerin yönetim bağlantı noktaları, tam zamanında ağ erişim denetimiyle korunmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |Olası ağ tam zamanında (JıT) erişim, Azure Güvenlik Merkezi tarafından öneriler olarak izlenir |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |

## <a name="remote-diagnostic-and-configuration-port-protection"></a>Uzaktan tanılama ve yapılandırma bağlantı noktası koruması

### <a name="access-to-network-equipment-is-physically-protected"></a>Ağ ekipmana erişim fiziksel olarak korunur.

**Kimlik**: 1192.01 l1organizational. 1-01. l **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Sanal makinelerin yönetim bağlantı noktaları, tam zamanında ağ erişim denetimiyle korunmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |Olası ağ tam zamanında (JıT) erişim, Azure Güvenlik Merkezi tarafından öneriler olarak izlenir |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |

### <a name="controls-for-the-access-to-diagnostic-and-configuration-ports-include-the-use-of-a-key-lock-and-the-implementation-of-supporting-procedures-to-control-physical-access-to-the-port"></a>Tanılama ve yapılandırma bağlantı noktalarına erişim denetimleri, anahtar kilidinin kullanımını ve bağlantı noktasına fiziksel erişimi denetlemek için destekleyici yordamların uygulanmasını içerir.

**Kimlik**: 1193.01 l2organizational. 13-01. l **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Yönetim bağlantı noktaları sanal makinelerinizde kapatılmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22730e10-96f6-4aac-ad84-9383d35b5917) |Açık Uzaktan yönetim bağlantı noktaları, sanal makinenizin Internet tabanlı saldırılardan yüksek düzeyde riske sunulmasını sağlar. Bu saldırılar, makineye yönetici erişimi kazanmak için zorlamalı kimlik bilgilerini yanılmaya çalışır. |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OpenManagementPortsOnVirtualMachines_Audit.json) |

### <a name="ports-services-and-similar-applications-installed-on-a-computer-or-network-systems-which-are-not-specifically-required-for-business-functionality-are-disabled-or-removed"></a>Bir bilgisayarda veya ağ sistemlerinde yüklü olan bağlantı noktaları, hizmetler ve benzer uygulamalar, özellikle de iş işlevselliği için gerekli değildir, devre dışıdır veya kaldırılır.

**Kimlik**: 1194.01 l2organizational. 2-01. l **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Web uygulamaları için uzaktan hata ayıklama kapatılmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcb510bfd-1cba-4d9f-a230-cb0976f4bb71) |Uzaktan hata ayıklama, gelen bağlantı noktalarının bir Web uygulamasında açılmasını gerektirir. Uzaktan hata ayıklama kapalı olmalıdır. |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_WebApp_Audit.json) |

### <a name="the-organization-reviews-the-information-system-within-every-three-hundred-and-sixty--five-365-days-to-identify-and-disables-unnecessary-and-non-secure-functions-ports-protocols-andor-services"></a>Kuruluş, gereksiz ve güvenli olmayan işlevleri, bağlantı noktalarını, protokolleri ve/veya hizmetleri tanımlamak ve devre dışı bırakmak için her 365 (365) gün içinde bilgi sistemini gözden geçirir.

**Kimlik**: 1195.01 l3organizational. 1-01. l **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Işlev uygulamaları için uzaktan hata ayıklama kapatılmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e60b895-3786-45da-8377-9c6b4b6ac5f9) |Uzaktan hata ayıklama, işlev uygulamalarında gelen bağlantı noktalarının açılmasını gerektirir. Uzaktan hata ayıklama kapalı olmalıdır. |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_FunctionApp_Audit.json) |

### <a name="the-organization-identifies-unauthorized-blacklisted-software-on-the-information-system-prevents-program-execution-in-accordance-with-a-list-of-unauthorized-blacklisted-software-programs-employs-an-allow-all-deny-by-exception-policy-to-prohibit-execution-of-known-unauthorized-blacklisted-software-and-reviews-and-updates-the-list-of-unauthorized-blacklisted-software-programs-annually"></a>Kuruluş, bilgi sisteminde yetkisiz (kara listelenmiş) yazılımları tanımlar, programın yürütülmesini yetkisiz (kara listelenen) yazılım programlarının bir listesiyle uyumlu olarak belirler, bilinen yetkisiz (kara listelenen) yazılımların yürütülmesini önlemek için bir izin verme ve reddetme özel durum ilkesi kullanır ve yetkisiz (kara listelenen) yazılım programlarının listesini yıllık olarak gözden geçirir ve güncelleştirir.

**Kimlik**: 1196.01 l3organizational. 24-01. l **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[API Apps için uzaktan hata ayıklama kapatılmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe9c8d085-d9cc-4b17-9cdc-059f1f01f19e) |Uzaktan hata ayıklama, API uygulamalarında gelen bağlantı noktalarının açılmasını gerektirir. Uzaktan hata ayıklama kapalı olmalıdır. |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_ApiApp_Audit.json) |

### <a name="the-organization-disables-bluetooth-and-peer-to-peer-networking-protocols-within-the-information-system-determined-to-be-unnecessary-or-non-secure"></a>Kuruluş, bilgi sistemi içindeki Bluetooth ve eşler arası ağ protokollerini devre dışı bırakarak, gereksiz veya güvenli olmayan bir şekilde belirlenir.

**Kimlik**: 1197.01 l3organizational. 3-01. l **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Güvenli uygulamaları tanımlamaya yönelik Uyarlamalı uygulama denetimleri, makinelerinizde etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |Makinelerinizde çalışan bilinen güvenli uygulamaların listesini tanımlamak için uygulama denetimlerini etkinleştirin ve diğer uygulamalar çalıştırıldığında sizi uyarır. Bu, makinelerinizi kötü amaçlı yazılımlara karşı korumanıza yardımcı olur. Güvenlik Merkezi, kurallarınızı yapılandırma ve sürdürme sürecini basitleştirmek için makine öğrenimini kullanarak her makinede çalışan uygulamaları analiz eder ve bilinen güvenli uygulamalar listesini önerir. |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |

## <a name="segregation-in-networks"></a>Ağlarda ayrım yapma

### <a name="the-organizations-security-gateways-eg-firewalls-enforce-security-policies-and-are-configured-to-filter-traffic-between-domains-block-unauthorized-access-and-are-used-to-maintain-segregation-between-internal-wired-internal-wireless-and-external-network-segments-eg-the-internet-including-dmzs-and-enforce-access-control-policies-for-each-of-the-domains"></a>Kuruluşun güvenlik ağ geçitleri (ör. güvenlik duvarları) güvenlik ilkelerini uygular ve etki alanları arasındaki trafiği filtrelemek, yetkisiz erişimi engellemek ve etki alanlarının her biri için, DMZs dahil olmak üzere iç kablolu, iç kablosuz ve dış ağ kesimleri (örn. Internet) arasında ayrımı sürdürmek için kullanılır.

**Kimlik**: 0805.01 m1organizational. 12-01. d **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[App Service bir sanal ağ hizmeti uç noktası kullanmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2d21331d-a4c2-4def-a9ad-ee4e1e023beb) |Bu ilke, bir sanal ağ hizmeti uç noktası kullanmak üzere yapılandırılmamış App Service denetler. |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_AppService_AuditIfNotExists.json) |
|[Container Registry bir sanal ağ hizmeti uç noktası kullanmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |Bu ilke, bir sanal ağ hizmeti uç noktası kullanmak üzere yapılandırılmamış Container Registry denetler. |Denetim, devre dışı |[1.0.0-Önizleme](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |
|[Cosmos DB bir sanal ağ hizmeti uç noktası kullanmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe0a2b1a3-f7f9-4569-807f-2a9edebdf4d9) |Bu ilke, bir sanal ağ hizmeti uç noktası kullanmak üzere yapılandırılmamış Cosmos DB denetler. |Denetim, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_CosmosDB_Audit.json) |
|[Olay Hub 'ı bir sanal ağ hizmeti uç noktası kullanmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd63edb4a-c612-454d-b47d-191a724fcbf0) |Bu ilke, bir sanal ağ hizmeti uç noktası kullanacak şekilde yapılandırılmamış tüm olay hub 'ını denetler. |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_EventHub_AuditIfNotExists.json) |
|[Ağ geçidi alt ağları bir ağ güvenlik grubuyla yapılandırılmamalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F35f9c03a-cc27-418e-9c0c-539ff999d010) |Bu ilke, bir ağ geçidi alt ağının bir ağ güvenlik grubuyla yapılandırılıp yapılandırılmadığını engeller. Ağ güvenlik grubunun ağ geçidi alt ağına atanması, ağ geçidinin çalışmayı durdurmasına neden olur. |reddedebilir |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkSecurityGroupOnGatewaySubnet_Deny.json) |
|[Internet 'e yönelik sanal makineler ağ güvenlik gruplarıyla korunmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |Ağ güvenlik grupları (NSG) ile erişimi kısıtlayarak sanal makinelerinizi olası tehditlere karşı koruyun. NSG 'ler ile trafiği denetleme hakkında daha fazla bilgi edinin [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[Key Vault bir sanal ağ hizmeti uç noktası kullanmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea4d6841-2173-4317-9747-ff522a45120f) |Bu ilke, bir sanal ağ hizmeti uç noktası kullanmak üzere yapılandırılmamış Key Vault denetler. |Denetim, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_KeyVault_Audit.json) |
|[Service Bus bir sanal ağ hizmeti uç noktası kullanmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F235359c5-7c52-4b82-9055-01c75cf9f60e) |Bu ilke, bir sanal ağ hizmeti uç noktası kullanmak üzere yapılandırılmamış Service Bus denetler. |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ServiceBus_AuditIfNotExists.json) |
|[SQL Server bir sanal ağ hizmeti uç noktası kullanmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fae5d2f14-d830-42b6-9899-df6cfe9c71a3) |Bu ilke, bir sanal ağ hizmeti uç noktası kullanmak üzere yapılandırılmamış SQL Server denetler. |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_SQLServer_AuditIfNotExists.json) |
|[Depolama hesapları bir sanal ağ hizmeti uç noktası kullanmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F60d21c4f-21a3-4d94-85f4-b924e6aeeda4) |Bu ilke, bir sanal ağ hizmeti uç noktası kullanmak üzere yapılandırılmamış tüm depolama hesaplarını denetler. |Denetim, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_StorageAccount_Audit.json) |
|[Alt ağlar bir ağ güvenlik grubuyla ilişkilendirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |Ağ güvenlik grubu (NSG) ile erişimi kısıtlayarak alt ağınızı olası tehditlere karşı koruyun. NSG 'ler, alt ağınıza ağ trafiğine izin veren veya reddeden Access Control listesi (ACL) kurallarının bir listesini içerir. |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[Sanal makinelerin onaylanan bir sanal ağa bağlanması gerekir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |Bu ilke, onaylanmamış bir sanal ağa bağlı tüm sanal makineleri denetler. |Denetim, reddetme, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |

### <a name="the-organizations-network-is-logically-and-physically-segmented-with-a-defined-security-perimeter-and-a-graduated-set-of-controls-including-subnetworks-for-publicly-accessible-system-components-that-are-logically-separated-from-the-internal-network-based-on-organizational-requirements-and-traffic-is-controlled-based-on-functionality-required-and-classification-of-the-datasystems-based-on-a-risk-assessment-and-their-respective-security-requirements"></a>Kuruluşlar ağı, kurumsal gereksinimlere bağlı olarak, iç ağdan mantıksal olarak ayrılan genel erişimli sistem bileşenlerine yönelik alt ağlar da dahil olmak üzere, tanımlı bir güvenlik çevresi ve dereceli bir denetim kümesi ile mantıksal ve fiziksel olarak bölünmüştür; ve trafik, risk değerlendirmesine ve bunların ilgili güvenlik gereksinimlerine bağlı olarak, gereken işlevlere ve veri/sistemlerin sınıflandırmasına göre denetlenir.

**Kimlik**: 0806.01 m2organizational. 12356-01. d **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[App Service bir sanal ağ hizmeti uç noktası kullanmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2d21331d-a4c2-4def-a9ad-ee4e1e023beb) |Bu ilke, bir sanal ağ hizmeti uç noktası kullanmak üzere yapılandırılmamış App Service denetler. |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_AppService_AuditIfNotExists.json) |
|[Container Registry bir sanal ağ hizmeti uç noktası kullanmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |Bu ilke, bir sanal ağ hizmeti uç noktası kullanmak üzere yapılandırılmamış Container Registry denetler. |Denetim, devre dışı |[1.0.0-Önizleme](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |
|[Cosmos DB bir sanal ağ hizmeti uç noktası kullanmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe0a2b1a3-f7f9-4569-807f-2a9edebdf4d9) |Bu ilke, bir sanal ağ hizmeti uç noktası kullanmak üzere yapılandırılmamış Cosmos DB denetler. |Denetim, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_CosmosDB_Audit.json) |
|[Olay Hub 'ı bir sanal ağ hizmeti uç noktası kullanmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd63edb4a-c612-454d-b47d-191a724fcbf0) |Bu ilke, bir sanal ağ hizmeti uç noktası kullanacak şekilde yapılandırılmamış tüm olay hub 'ını denetler. |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_EventHub_AuditIfNotExists.json) |
|[Ağ geçidi alt ağları bir ağ güvenlik grubuyla yapılandırılmamalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F35f9c03a-cc27-418e-9c0c-539ff999d010) |Bu ilke, bir ağ geçidi alt ağının bir ağ güvenlik grubuyla yapılandırılıp yapılandırılmadığını engeller. Ağ güvenlik grubunun ağ geçidi alt ağına atanması, ağ geçidinin çalışmayı durdurmasına neden olur. |reddedebilir |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkSecurityGroupOnGatewaySubnet_Deny.json) |
|[Internet 'e yönelik sanal makineler ağ güvenlik gruplarıyla korunmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |Ağ güvenlik grupları (NSG) ile erişimi kısıtlayarak sanal makinelerinizi olası tehditlere karşı koruyun. NSG 'ler ile trafiği denetleme hakkında daha fazla bilgi edinin [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[Key Vault bir sanal ağ hizmeti uç noktası kullanmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea4d6841-2173-4317-9747-ff522a45120f) |Bu ilke, bir sanal ağ hizmeti uç noktası kullanmak üzere yapılandırılmamış Key Vault denetler. |Denetim, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_KeyVault_Audit.json) |
|[Service Bus bir sanal ağ hizmeti uç noktası kullanmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F235359c5-7c52-4b82-9055-01c75cf9f60e) |Bu ilke, bir sanal ağ hizmeti uç noktası kullanmak üzere yapılandırılmamış Service Bus denetler. |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ServiceBus_AuditIfNotExists.json) |
|[SQL Server bir sanal ağ hizmeti uç noktası kullanmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fae5d2f14-d830-42b6-9899-df6cfe9c71a3) |Bu ilke, bir sanal ağ hizmeti uç noktası kullanmak üzere yapılandırılmamış SQL Server denetler. |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_SQLServer_AuditIfNotExists.json) |
|[Depolama hesapları bir sanal ağ hizmeti uç noktası kullanmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F60d21c4f-21a3-4d94-85f4-b924e6aeeda4) |Bu ilke, bir sanal ağ hizmeti uç noktası kullanmak üzere yapılandırılmamış tüm depolama hesaplarını denetler. |Denetim, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_StorageAccount_Audit.json) |
|[Alt ağlar bir ağ güvenlik grubuyla ilişkilendirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |Ağ güvenlik grubu (NSG) ile erişimi kısıtlayarak alt ağınızı olası tehditlere karşı koruyun. NSG 'ler, alt ağınıza ağ trafiğine izin veren veya reddeden Access Control listesi (ACL) kurallarının bir listesini içerir. |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[Sanal makinelerin onaylanan bir sanal ağa bağlanması gerekir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |Bu ilke, onaylanmamış bir sanal ağa bağlı tüm sanal makineleri denetler. |Denetim, reddetme, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |

### <a name="networks-are-segregated-from-production-level-networks-when-migrating-physical-servers-applications-or-data-to-virtualized-servers"></a>Ağlar, fiziksel sunucular, uygulamalar veya veriler sanallaştırılmış sunuculara geçirilirken üretim düzeyindeki ağlardan ayırabilirsiniz.

**Kimlik**: 0894.01 m2organizational. 7-01. d **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[App Service bir sanal ağ hizmeti uç noktası kullanmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2d21331d-a4c2-4def-a9ad-ee4e1e023beb) |Bu ilke, bir sanal ağ hizmeti uç noktası kullanmak üzere yapılandırılmamış App Service denetler. |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_AppService_AuditIfNotExists.json) |
|[Container Registry bir sanal ağ hizmeti uç noktası kullanmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |Bu ilke, bir sanal ağ hizmeti uç noktası kullanmak üzere yapılandırılmamış Container Registry denetler. |Denetim, devre dışı |[1.0.0-Önizleme](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |
|[Cosmos DB bir sanal ağ hizmeti uç noktası kullanmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe0a2b1a3-f7f9-4569-807f-2a9edebdf4d9) |Bu ilke, bir sanal ağ hizmeti uç noktası kullanmak üzere yapılandırılmamış Cosmos DB denetler. |Denetim, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_CosmosDB_Audit.json) |
|[Sanal ağlar oluşturulduğunda Ağ İzleyicisi dağıt](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa9b99dd8-06c5-4317-8629-9d86a3c6e7d9) |Bu ilke, sanal ağları olan bölgelerde bir ağ izleyicisi kaynağı oluşturur. Ağ İzleyicisi örneklerini dağıtmak için kullanılacak networkWatcherRG adlı bir kaynak grubunun varlığını güvence altına almanız gerekir. |DeployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Deploy.json) |
|[Olay Hub 'ı bir sanal ağ hizmeti uç noktası kullanmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd63edb4a-c612-454d-b47d-191a724fcbf0) |Bu ilke, bir sanal ağ hizmeti uç noktası kullanacak şekilde yapılandırılmamış tüm olay hub 'ını denetler. |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_EventHub_AuditIfNotExists.json) |
|[Ağ geçidi alt ağları bir ağ güvenlik grubuyla yapılandırılmamalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F35f9c03a-cc27-418e-9c0c-539ff999d010) |Bu ilke, bir ağ geçidi alt ağının bir ağ güvenlik grubuyla yapılandırılıp yapılandırılmadığını engeller. Ağ güvenlik grubunun ağ geçidi alt ağına atanması, ağ geçidinin çalışmayı durdurmasına neden olur. |reddedebilir |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkSecurityGroupOnGatewaySubnet_Deny.json) |
|[Internet 'e yönelik sanal makineler ağ güvenlik gruplarıyla korunmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |Ağ güvenlik grupları (NSG) ile erişimi kısıtlayarak sanal makinelerinizi olası tehditlere karşı koruyun. NSG 'ler ile trafiği denetleme hakkında daha fazla bilgi edinin [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[Key Vault bir sanal ağ hizmeti uç noktası kullanmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea4d6841-2173-4317-9747-ff522a45120f) |Bu ilke, bir sanal ağ hizmeti uç noktası kullanmak üzere yapılandırılmamış Key Vault denetler. |Denetim, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_KeyVault_Audit.json) |
|[Service Bus bir sanal ağ hizmeti uç noktası kullanmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F235359c5-7c52-4b82-9055-01c75cf9f60e) |Bu ilke, bir sanal ağ hizmeti uç noktası kullanmak üzere yapılandırılmamış Service Bus denetler. |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ServiceBus_AuditIfNotExists.json) |
|[SQL Server bir sanal ağ hizmeti uç noktası kullanmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fae5d2f14-d830-42b6-9899-df6cfe9c71a3) |Bu ilke, bir sanal ağ hizmeti uç noktası kullanmak üzere yapılandırılmamış SQL Server denetler. |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_SQLServer_AuditIfNotExists.json) |
|[Depolama hesapları bir sanal ağ hizmeti uç noktası kullanmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F60d21c4f-21a3-4d94-85f4-b924e6aeeda4) |Bu ilke, bir sanal ağ hizmeti uç noktası kullanmak üzere yapılandırılmamış tüm depolama hesaplarını denetler. |Denetim, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_StorageAccount_Audit.json) |
|[Alt ağlar bir ağ güvenlik grubuyla ilişkilendirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |Ağ güvenlik grubu (NSG) ile erişimi kısıtlayarak alt ağınızı olası tehditlere karşı koruyun. NSG 'ler, alt ağınıza ağ trafiğine izin veren veya reddeden Access Control listesi (ACL) kurallarının bir listesini içerir. |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[Sanal makinelerin onaylanan bir sanal ağa bağlanması gerekir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |Bu ilke, onaylanmamış bir sanal ağa bağlı tüm sanal makineleri denetler. |Denetim, reddetme, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |

## <a name="network-connection-control"></a>Ağ bağlantısı denetimi

### <a name="network-traffic-is-controlled-in-accordance-with-the-organizations-access-control-policy-through-firewall-and-other-network-related-restrictions-for-each-network-access-point-or-external-telecommunication-services-managed-interface"></a>Ağ trafiği, güvenlik duvarı ve her bir ağ erişim noktası ya da harici telekomünikasyon hizmeti 'nin yönetilen arabirimi için ağ ile ilgili diğer kısıtlamalar aracılığıyla kuruluşların erişim denetim ilkesine uygun olarak denetlenir.

**Kimlik**: 0809.01 n2organizational. 1234-01. n **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Uyarlamalı ağ sağlamlaştırma önerileri internet 'e yönelik sanal makinelere uygulanmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure Güvenlik Merkezi, Internet 'e yönelik sanal makinelerin trafik düzenlerini analiz eder ve olası saldırı yüzeyini azaltan ağ güvenlik grubu kuralı önerileri sağlar |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |
|[API uygulaması yalnızca HTTPS üzerinden erişilebilir olmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |HTTPS kullanımı, sunucu/hizmet kimlik doğrulamasını sağlar ve ağ katmanı gizlice dinleme saldırılarına karşı geçiş sırasında verileri korur. |Denetim, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |
|[MySQL veritabanı sunucuları için SSL bağlantısını zorla etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |MySQL için Azure veritabanı, MySQL Server için Azure veritabanı sunucunuzu Güvenli Yuva Katmanı (SSL) kullanarak istemci uygulamalarına bağlamayı destekler. Veritabanı sunucunuz ile istemci uygulamalarınız arasında SSL bağlantılarını zorunlu tutma, sunucu ile uygulamanız arasındaki veri akışını şifreleyerek, ortadaki adam saldırılarına karşı korunmaya yardımcı olur. Bu yapılandırma, veritabanı sunucunuza erişmek için SSL 'nin her zaman etkinleştirilmesini zorunlu kılar. |Denetim, devre dışı |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |
|[PostgreSQL veritabanı sunucuları için SSL bağlantısını zorla etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |PostgreSQL için Azure veritabanı, PostgreSQL için Azure veritabanı sunucunuzu Güvenli Yuva Katmanı (SSL) kullanarak istemci uygulamalarına bağlamayı destekler. Veritabanı sunucunuz ile istemci uygulamalarınız arasında SSL bağlantılarını zorunlu tutma, sunucu ile uygulamanız arasındaki veri akışını şifreleyerek, ortadaki adam saldırılarına karşı korunmaya yardımcı olur. Bu yapılandırma, veritabanı sunucunuza erişmek için SSL 'nin her zaman etkinleştirilmesini zorunlu kılar. |Denetim, devre dışı |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |
|[İşlev Uygulaması yalnızca HTTPS üzerinden erişilebilir olmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |HTTPS kullanımı, sunucu/hizmet kimlik doğrulamasını sağlar ve ağ katmanı gizlice dinleme saldırılarına karşı geçiş sırasında verileri korur. |Denetim, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |
|[Internet 'e yönelik sanal makineler ağ güvenlik gruplarıyla korunmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |Ağ güvenlik grupları (NSG) ile erişimi kısıtlayarak sanal makinelerinizi olası tehditlere karşı koruyun. NSG 'ler ile trafiği denetleme hakkında daha fazla bilgi edinin [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[API uygulamanızda en son TLS sürümü kullanılmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |En son TLS sürümüne yükselt |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[İşlev Uygulaması en son TLS sürümü kullanılmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |En son TLS sürümüne yükselt |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[Web uygulamanızda en son TLS sürümü kullanılmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |En son TLS sürümüne yükselt |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |
|[Redsıs için yalnızca Azure önbelleğinize güvenli bağlantılar etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Redsıs için yalnızca SSL ile SSL aracılığıyla bağlantıların etkinleştirilmesini denetleme. Güvenli bağlantı kullanımı, sunucu ve hizmet arasında kimlik doğrulaması sağlar ve geçiş sırasında ortadaki adam, gizlice dinleme ve oturum ele geçirme gibi ağ katmanı saldırılarına karşı verileri korur |Denetim, reddetme, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|[Depolama hesaplarına güvenli aktarım etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |Depolama hesabınızda güvenli aktarım gereksinimini denetleyin. Güvenli aktarım, depolama hesabınızı yalnızca güvenli bağlantılardan (HTTPS) istekleri kabul edecek şekilde zorlayan bir seçenektir. HTTPS kullanımı, sunucu ile hizmet arasında kimlik doğrulaması sağlar ve geçiş sırasında ortadaki adam, gizlice dinleme ve oturum ele geçirme gibi ağ katmanı saldırılarına karşı verileri korur |Denetim, reddetme, devre dışı |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |
|[Alt ağlar bir ağ güvenlik grubuyla ilişkilendirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |Ağ güvenlik grubu (NSG) ile erişimi kısıtlayarak alt ağınızı olası tehditlere karşı koruyun. NSG 'ler, alt ağınıza ağ trafiğine izin veren veya reddeden Access Control listesi (ACL) kurallarının bir listesini içerir. |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[Sanal makinelerin onaylanan bir sanal ağa bağlanması gerekir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |Bu ilke, onaylanmamış bir sanal ağa bağlı tüm sanal makineleri denetler. |Denetim, reddetme, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |
|[Web uygulaması yalnızca HTTPS üzerinden erişilebilir olmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |HTTPS kullanımı, sunucu/hizmet kimlik doğrulamasını sağlar ve ağ katmanı gizlice dinleme saldırılarına karşı geçiş sırasında verileri korur. |Denetim, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

### <a name="transmitted-information-is-secured-and-at-a-minimum-encrypted-over-open-public-networks"></a>İletilen bilgiler güvenlidir ve en azından açık, ortak ağlarda şifrelenir.

**Kimlik**: 0810.01 n2organizational. 5-01. n **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Uyarlamalı ağ sağlamlaştırma önerileri internet 'e yönelik sanal makinelere uygulanmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure Güvenlik Merkezi, Internet 'e yönelik sanal makinelerin trafik düzenlerini analiz eder ve olası saldırı yüzeyini azaltan ağ güvenlik grubu kuralı önerileri sağlar |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |
|[API uygulaması yalnızca HTTPS üzerinden erişilebilir olmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |HTTPS kullanımı, sunucu/hizmet kimlik doğrulamasını sağlar ve ağ katmanı gizlice dinleme saldırılarına karşı geçiş sırasında verileri korur. |Denetim, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |
|[MySQL veritabanı sunucuları için SSL bağlantısını zorla etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |MySQL için Azure veritabanı, MySQL Server için Azure veritabanı sunucunuzu Güvenli Yuva Katmanı (SSL) kullanarak istemci uygulamalarına bağlamayı destekler. Veritabanı sunucunuz ile istemci uygulamalarınız arasında SSL bağlantılarını zorunlu tutma, sunucu ile uygulamanız arasındaki veri akışını şifreleyerek, ortadaki adam saldırılarına karşı korunmaya yardımcı olur. Bu yapılandırma, veritabanı sunucunuza erişmek için SSL 'nin her zaman etkinleştirilmesini zorunlu kılar. |Denetim, devre dışı |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |
|[PostgreSQL veritabanı sunucuları için SSL bağlantısını zorla etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |PostgreSQL için Azure veritabanı, PostgreSQL için Azure veritabanı sunucunuzu Güvenli Yuva Katmanı (SSL) kullanarak istemci uygulamalarına bağlamayı destekler. Veritabanı sunucunuz ile istemci uygulamalarınız arasında SSL bağlantılarını zorunlu tutma, sunucu ile uygulamanız arasındaki veri akışını şifreleyerek, ortadaki adam saldırılarına karşı korunmaya yardımcı olur. Bu yapılandırma, veritabanı sunucunuza erişmek için SSL 'nin her zaman etkinleştirilmesini zorunlu kılar. |Denetim, devre dışı |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |
|[İşlev Uygulaması yalnızca HTTPS üzerinden erişilebilir olmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |HTTPS kullanımı, sunucu/hizmet kimlik doğrulamasını sağlar ve ağ katmanı gizlice dinleme saldırılarına karşı geçiş sırasında verileri korur. |Denetim, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |
|[Internet 'e yönelik sanal makineler ağ güvenlik gruplarıyla korunmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |Ağ güvenlik grupları (NSG) ile erişimi kısıtlayarak sanal makinelerinizi olası tehditlere karşı koruyun. NSG 'ler ile trafiği denetleme hakkında daha fazla bilgi edinin [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[API uygulamanızda en son TLS sürümü kullanılmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |En son TLS sürümüne yükselt |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[İşlev Uygulaması en son TLS sürümü kullanılmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |En son TLS sürümüne yükselt |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[Web uygulamanızda en son TLS sürümü kullanılmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |En son TLS sürümüne yükselt |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |
|[Redsıs için yalnızca Azure önbelleğinize güvenli bağlantılar etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Redsıs için yalnızca SSL ile SSL aracılığıyla bağlantıların etkinleştirilmesini denetleme. Güvenli bağlantı kullanımı, sunucu ve hizmet arasında kimlik doğrulaması sağlar ve geçiş sırasında ortadaki adam, gizlice dinleme ve oturum ele geçirme gibi ağ katmanı saldırılarına karşı verileri korur |Denetim, reddetme, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|[Depolama hesaplarına güvenli aktarım etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |Depolama hesabınızda güvenli aktarım gereksinimini denetleyin. Güvenli aktarım, depolama hesabınızı yalnızca güvenli bağlantılardan (HTTPS) istekleri kabul edecek şekilde zorlayan bir seçenektir. HTTPS kullanımı, sunucu ile hizmet arasında kimlik doğrulaması sağlar ve geçiş sırasında ortadaki adam, gizlice dinleme ve oturum ele geçirme gibi ağ katmanı saldırılarına karşı verileri korur |Denetim, reddetme, devre dışı |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |
|[Alt ağlar bir ağ güvenlik grubuyla ilişkilendirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |Ağ güvenlik grubu (NSG) ile erişimi kısıtlayarak alt ağınızı olası tehditlere karşı koruyun. NSG 'ler, alt ağınıza ağ trafiğine izin veren veya reddeden Access Control listesi (ACL) kurallarının bir listesini içerir. |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[Sanal makinelerin onaylanan bir sanal ağa bağlanması gerekir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |Bu ilke, onaylanmamış bir sanal ağa bağlı tüm sanal makineleri denetler. |Denetim, reddetme, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |
|[Web uygulaması yalnızca HTTPS üzerinden erişilebilir olmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |HTTPS kullanımı, sunucu/hizmet kimlik doğrulamasını sağlar ve ağ katmanı gizlice dinleme saldırılarına karşı geçiş sırasında verileri korur. |Denetim, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

### <a name="exceptions-to-the-traffic-flow-policy-are-documented-with-a-supporting-missionbusiness-need-duration-of-the-exception-and-reviewed-at-least-annually-traffic-flow-policy-exceptions-are-removed-when-no-longer-supported-by-an-explicit-missionbusiness-need"></a>Trafik akışı ilkesi için özel durumlar, bir destekleme görevi/iş ihtiyacı, özel durum süresi ve en az yılda bir şekilde incelendi. trafik akışı ilkesi özel durumları artık açık bir görev/iş ihtiyacı tarafından desteklenmeden kaldırılır.

**Kimlik**: 0811.01 n2organizational. 6-01. n **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Uyarlamalı ağ sağlamlaştırma önerileri internet 'e yönelik sanal makinelere uygulanmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure Güvenlik Merkezi, Internet 'e yönelik sanal makinelerin trafik düzenlerini analiz eder ve olası saldırı yüzeyini azaltan ağ güvenlik grubu kuralı önerileri sağlar |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |
|[API uygulaması yalnızca HTTPS üzerinden erişilebilir olmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |HTTPS kullanımı, sunucu/hizmet kimlik doğrulamasını sağlar ve ağ katmanı gizlice dinleme saldırılarına karşı geçiş sırasında verileri korur. |Denetim, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |
|[MySQL veritabanı sunucuları için SSL bağlantısını zorla etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |MySQL için Azure veritabanı, MySQL Server için Azure veritabanı sunucunuzu Güvenli Yuva Katmanı (SSL) kullanarak istemci uygulamalarına bağlamayı destekler. Veritabanı sunucunuz ile istemci uygulamalarınız arasında SSL bağlantılarını zorunlu tutma, sunucu ile uygulamanız arasındaki veri akışını şifreleyerek, ortadaki adam saldırılarına karşı korunmaya yardımcı olur. Bu yapılandırma, veritabanı sunucunuza erişmek için SSL 'nin her zaman etkinleştirilmesini zorunlu kılar. |Denetim, devre dışı |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |
|[PostgreSQL veritabanı sunucuları için SSL bağlantısını zorla etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |PostgreSQL için Azure veritabanı, PostgreSQL için Azure veritabanı sunucunuzu Güvenli Yuva Katmanı (SSL) kullanarak istemci uygulamalarına bağlamayı destekler. Veritabanı sunucunuz ile istemci uygulamalarınız arasında SSL bağlantılarını zorunlu tutma, sunucu ile uygulamanız arasındaki veri akışını şifreleyerek, ortadaki adam saldırılarına karşı korunmaya yardımcı olur. Bu yapılandırma, veritabanı sunucunuza erişmek için SSL 'nin her zaman etkinleştirilmesini zorunlu kılar. |Denetim, devre dışı |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |
|[İşlev Uygulaması yalnızca HTTPS üzerinden erişilebilir olmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |HTTPS kullanımı, sunucu/hizmet kimlik doğrulamasını sağlar ve ağ katmanı gizlice dinleme saldırılarına karşı geçiş sırasında verileri korur. |Denetim, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |
|[Internet 'e yönelik sanal makineler ağ güvenlik gruplarıyla korunmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |Ağ güvenlik grupları (NSG) ile erişimi kısıtlayarak sanal makinelerinizi olası tehditlere karşı koruyun. NSG 'ler ile trafiği denetleme hakkında daha fazla bilgi edinin [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[API uygulamanızda en son TLS sürümü kullanılmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |En son TLS sürümüne yükselt |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[İşlev Uygulaması en son TLS sürümü kullanılmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |En son TLS sürümüne yükselt |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[Web uygulamanızda en son TLS sürümü kullanılmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |En son TLS sürümüne yükselt |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |
|[Redsıs için yalnızca Azure önbelleğinize güvenli bağlantılar etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Redsıs için yalnızca SSL ile SSL aracılığıyla bağlantıların etkinleştirilmesini denetleme. Güvenli bağlantı kullanımı, sunucu ve hizmet arasında kimlik doğrulaması sağlar ve geçiş sırasında ortadaki adam, gizlice dinleme ve oturum ele geçirme gibi ağ katmanı saldırılarına karşı verileri korur |Denetim, reddetme, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|[Depolama hesaplarına güvenli aktarım etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |Depolama hesabınızda güvenli aktarım gereksinimini denetleyin. Güvenli aktarım, depolama hesabınızı yalnızca güvenli bağlantılardan (HTTPS) istekleri kabul edecek şekilde zorlayan bir seçenektir. HTTPS kullanımı, sunucu ile hizmet arasında kimlik doğrulaması sağlar ve geçiş sırasında ortadaki adam, gizlice dinleme ve oturum ele geçirme gibi ağ katmanı saldırılarına karşı verileri korur |Denetim, reddetme, devre dışı |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |
|[Alt ağlar bir ağ güvenlik grubuyla ilişkilendirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |Ağ güvenlik grubu (NSG) ile erişimi kısıtlayarak alt ağınızı olası tehditlere karşı koruyun. NSG 'ler, alt ağınıza ağ trafiğine izin veren veya reddeden Access Control listesi (ACL) kurallarının bir listesini içerir. |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[Sanal makinelerin onaylanan bir sanal ağa bağlanması gerekir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |Bu ilke, onaylanmamış bir sanal ağa bağlı tüm sanal makineleri denetler. |Denetim, reddetme, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |
|[Web uygulaması yalnızca HTTPS üzerinden erişilebilir olmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |HTTPS kullanımı, sunucu/hizmet kimlik doğrulamasını sağlar ve ağ katmanı gizlice dinleme saldırılarına karşı geçiş sırasında verileri korur. |Denetim, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

### <a name="remote-devices-establishing-a-non-remote-connection-are-not-allowed-to-communicate-with-external-remote-resources"></a>Uzak cihazların uzak olmayan bir bağlantı kurmaya, dış (uzak) kaynaklarla iletişim kurmasına izin verilmez.

**Kimlik**: 0812.01 n2organizational. 8-01. n **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Uyarlamalı ağ sağlamlaştırma önerileri internet 'e yönelik sanal makinelere uygulanmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure Güvenlik Merkezi, Internet 'e yönelik sanal makinelerin trafik düzenlerini analiz eder ve olası saldırı yüzeyini azaltan ağ güvenlik grubu kuralı önerileri sağlar |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |
|[API uygulaması yalnızca HTTPS üzerinden erişilebilir olmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |HTTPS kullanımı, sunucu/hizmet kimlik doğrulamasını sağlar ve ağ katmanı gizlice dinleme saldırılarına karşı geçiş sırasında verileri korur. |Denetim, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |
|[MySQL veritabanı sunucuları için SSL bağlantısını zorla etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |MySQL için Azure veritabanı, MySQL Server için Azure veritabanı sunucunuzu Güvenli Yuva Katmanı (SSL) kullanarak istemci uygulamalarına bağlamayı destekler. Veritabanı sunucunuz ile istemci uygulamalarınız arasında SSL bağlantılarını zorunlu tutma, sunucu ile uygulamanız arasındaki veri akışını şifreleyerek, ortadaki adam saldırılarına karşı korunmaya yardımcı olur. Bu yapılandırma, veritabanı sunucunuza erişmek için SSL 'nin her zaman etkinleştirilmesini zorunlu kılar. |Denetim, devre dışı |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |
|[PostgreSQL veritabanı sunucuları için SSL bağlantısını zorla etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |PostgreSQL için Azure veritabanı, PostgreSQL için Azure veritabanı sunucunuzu Güvenli Yuva Katmanı (SSL) kullanarak istemci uygulamalarına bağlamayı destekler. Veritabanı sunucunuz ile istemci uygulamalarınız arasında SSL bağlantılarını zorunlu tutma, sunucu ile uygulamanız arasındaki veri akışını şifreleyerek, ortadaki adam saldırılarına karşı korunmaya yardımcı olur. Bu yapılandırma, veritabanı sunucunuza erişmek için SSL 'nin her zaman etkinleştirilmesini zorunlu kılar. |Denetim, devre dışı |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |
|[İşlev Uygulaması yalnızca HTTPS üzerinden erişilebilir olmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |HTTPS kullanımı, sunucu/hizmet kimlik doğrulamasını sağlar ve ağ katmanı gizlice dinleme saldırılarına karşı geçiş sırasında verileri korur. |Denetim, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |
|[Internet 'e yönelik sanal makineler ağ güvenlik gruplarıyla korunmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |Ağ güvenlik grupları (NSG) ile erişimi kısıtlayarak sanal makinelerinizi olası tehditlere karşı koruyun. NSG 'ler ile trafiği denetleme hakkında daha fazla bilgi edinin [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[API uygulamanızda en son TLS sürümü kullanılmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |En son TLS sürümüne yükselt |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[İşlev Uygulaması en son TLS sürümü kullanılmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |En son TLS sürümüne yükselt |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[Web uygulamanızda en son TLS sürümü kullanılmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |En son TLS sürümüne yükselt |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |
|[Redsıs için yalnızca Azure önbelleğinize güvenli bağlantılar etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Redsıs için yalnızca SSL ile SSL aracılığıyla bağlantıların etkinleştirilmesini denetleme. Güvenli bağlantı kullanımı, sunucu ve hizmet arasında kimlik doğrulaması sağlar ve geçiş sırasında ortadaki adam, gizlice dinleme ve oturum ele geçirme gibi ağ katmanı saldırılarına karşı verileri korur |Denetim, reddetme, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|[Depolama hesaplarına güvenli aktarım etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |Depolama hesabınızda güvenli aktarım gereksinimini denetleyin. Güvenli aktarım, depolama hesabınızı yalnızca güvenli bağlantılardan (HTTPS) istekleri kabul edecek şekilde zorlayan bir seçenektir. HTTPS kullanımı, sunucu ile hizmet arasında kimlik doğrulaması sağlar ve geçiş sırasında ortadaki adam, gizlice dinleme ve oturum ele geçirme gibi ağ katmanı saldırılarına karşı verileri korur |Denetim, reddetme, devre dışı |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |
|[Alt ağlar bir ağ güvenlik grubuyla ilişkilendirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |Ağ güvenlik grubu (NSG) ile erişimi kısıtlayarak alt ağınızı olası tehditlere karşı koruyun. NSG 'ler, alt ağınıza ağ trafiğine izin veren veya reddeden Access Control listesi (ACL) kurallarının bir listesini içerir. |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[Sanal makinelerin onaylanan bir sanal ağa bağlanması gerekir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |Bu ilke, onaylanmamış bir sanal ağa bağlı tüm sanal makineleri denetler. |Denetim, reddetme, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |
|[Web uygulaması yalnızca HTTPS üzerinden erişilebilir olmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |HTTPS kullanımı, sunucu/hizmet kimlik doğrulamasını sağlar ve ağ katmanı gizlice dinleme saldırılarına karşı geçiş sırasında verileri korur. |Denetim, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

### <a name="the-ability-of-users-to-connect-to-the-internal-network-is-restricted-using-a-deny-by-default-and-allow-by-exception-policy-at-managed-interfaces-according-to-the-access-control-policy-and-the-requirements-of-clinical-and-business-applications"></a>Kullanıcıların iç ağa bağlanma yeteneği, yönetilen arabirimlerde erişim denetim ilkesine ve klinik ve iş uygulamalarının gereksinimlerine göre, varsayılan olarak Reddet ve özel durum ilkesiyle izin ver ilkesi kullanılarak kısıtlıdır.

**Kimlik**: 0814.01 n1organizational. 12-01. n **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Uyarlamalı ağ sağlamlaştırma önerileri internet 'e yönelik sanal makinelere uygulanmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure Güvenlik Merkezi, Internet 'e yönelik sanal makinelerin trafik düzenlerini analiz eder ve olası saldırı yüzeyini azaltan ağ güvenlik grubu kuralı önerileri sağlar |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |
|[API uygulaması yalnızca HTTPS üzerinden erişilebilir olmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |HTTPS kullanımı, sunucu/hizmet kimlik doğrulamasını sağlar ve ağ katmanı gizlice dinleme saldırılarına karşı geçiş sırasında verileri korur. |Denetim, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |
|[MySQL veritabanı sunucuları için SSL bağlantısını zorla etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |MySQL için Azure veritabanı, MySQL Server için Azure veritabanı sunucunuzu Güvenli Yuva Katmanı (SSL) kullanarak istemci uygulamalarına bağlamayı destekler. Veritabanı sunucunuz ile istemci uygulamalarınız arasında SSL bağlantılarını zorunlu tutma, sunucu ile uygulamanız arasındaki veri akışını şifreleyerek, ortadaki adam saldırılarına karşı korunmaya yardımcı olur. Bu yapılandırma, veritabanı sunucunuza erişmek için SSL 'nin her zaman etkinleştirilmesini zorunlu kılar. |Denetim, devre dışı |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |
|[PostgreSQL veritabanı sunucuları için SSL bağlantısını zorla etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |PostgreSQL için Azure veritabanı, PostgreSQL için Azure veritabanı sunucunuzu Güvenli Yuva Katmanı (SSL) kullanarak istemci uygulamalarına bağlamayı destekler. Veritabanı sunucunuz ile istemci uygulamalarınız arasında SSL bağlantılarını zorunlu tutma, sunucu ile uygulamanız arasındaki veri akışını şifreleyerek, ortadaki adam saldırılarına karşı korunmaya yardımcı olur. Bu yapılandırma, veritabanı sunucunuza erişmek için SSL 'nin her zaman etkinleştirilmesini zorunlu kılar. |Denetim, devre dışı |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |
|[İşlev Uygulaması yalnızca HTTPS üzerinden erişilebilir olmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |HTTPS kullanımı, sunucu/hizmet kimlik doğrulamasını sağlar ve ağ katmanı gizlice dinleme saldırılarına karşı geçiş sırasında verileri korur. |Denetim, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |
|[Internet 'e yönelik sanal makineler ağ güvenlik gruplarıyla korunmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |Ağ güvenlik grupları (NSG) ile erişimi kısıtlayarak sanal makinelerinizi olası tehditlere karşı koruyun. NSG 'ler ile trafiği denetleme hakkında daha fazla bilgi edinin [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[API uygulamanızda en son TLS sürümü kullanılmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |En son TLS sürümüne yükselt |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[İşlev Uygulaması en son TLS sürümü kullanılmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |En son TLS sürümüne yükselt |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[Web uygulamanızda en son TLS sürümü kullanılmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |En son TLS sürümüne yükselt |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |
|[Redsıs için yalnızca Azure önbelleğinize güvenli bağlantılar etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Redsıs için yalnızca SSL ile SSL aracılığıyla bağlantıların etkinleştirilmesini denetleme. Güvenli bağlantı kullanımı, sunucu ve hizmet arasında kimlik doğrulaması sağlar ve geçiş sırasında ortadaki adam, gizlice dinleme ve oturum ele geçirme gibi ağ katmanı saldırılarına karşı verileri korur |Denetim, reddetme, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|[Depolama hesaplarına güvenli aktarım etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |Depolama hesabınızda güvenli aktarım gereksinimini denetleyin. Güvenli aktarım, depolama hesabınızı yalnızca güvenli bağlantılardan (HTTPS) istekleri kabul edecek şekilde zorlayan bir seçenektir. HTTPS kullanımı, sunucu ile hizmet arasında kimlik doğrulaması sağlar ve geçiş sırasında ortadaki adam, gizlice dinleme ve oturum ele geçirme gibi ağ katmanı saldırılarına karşı verileri korur |Denetim, reddetme, devre dışı |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |
|[Alt ağlar bir ağ güvenlik grubuyla ilişkilendirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |Ağ güvenlik grubu (NSG) ile erişimi kısıtlayarak alt ağınızı olası tehditlere karşı koruyun. NSG 'ler, alt ağınıza ağ trafiğine izin veren veya reddeden Access Control listesi (ACL) kurallarının bir listesini içerir. |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[Sanal makinelerin onaylanan bir sanal ağa bağlanması gerekir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |Bu ilke, onaylanmamış bir sanal ağa bağlı tüm sanal makineleri denetler. |Denetim, reddetme, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |
|[Web uygulaması yalnızca HTTPS üzerinden erişilebilir olmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |HTTPS kullanımı, sunucu/hizmet kimlik doğrulamasını sağlar ve ağ katmanı gizlice dinleme saldırılarına karşı geçiş sırasında verileri korur. |Denetim, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

## <a name="user-identification-and-authentication"></a>Kullanıcı kimliği ve kimlik doğrulaması

### <a name="the-organization-ensures-that-redundant-user-ids-are-not-issued-to-other-users-and-that-all-users-are-uniquely-identified-and-authenticated-for-both-local-and-remote-access-to-information-systems"></a>Kuruluş, ek kullanıcı kimliklerinin diğer kullanıcılara verilmemesini ve tüm kullanıcıların hem yerel hem de bilgi sistemlerine uzaktan erişim için benzersiz şekilde tanımlanmasını ve kimlik doğrulamasından ulaşmasını sağlar.

**Kimlik**: 11109.01 q1organizational. 57-01. q **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[MFA, aboneliğinizde sahip izinleri olan hesaplarda etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |Multi-Factor Authentication (MFA), hesapların veya kaynakların ihlaline engel olmak için sahip izinleri olan tüm abonelik hesapları için etkinleştirilmelidir. |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |

### <a name="non-organizational-users-all-information-system-users-other-than-organizational-users-such-as-patients-customers-contractors-or-foreign-nationals-or-processes-acting-on-behalf-of-non-organizational-users-determined-to-need-access-to-information-residing-on-the-organizations-information-systems-are-uniquely-identified-and-authenticated"></a>Kurumsal olmayan kullanıcılar (hastalar, müşteriler, yükleniciler veya yabancı ülke gibi kuruluş kullanıcıları dışındaki tüm bilgi sistem kullanıcıları) veya kurumsal olmayan kullanıcılar adına işlem yapan süreçler, kuruluşun bilgi sistemlerinde bulunan bilgilere erişime ihtiyacı olacak şekilde belirlenir ve kimlik doğrulanır.

**Kimlik**: 11110.01 q1organizational. 6-01. q **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Aboneliğinizde yazma izinleri olan hesaplarda MFA etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |Hesapların veya kaynakların ihlal oluşmasını engellemek için yazma ayrıcalıklarına sahip tüm abonelik hesapları için Multi-Factor Authentication (MFA) etkinleştirilmelidir. |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |

### <a name="when-pki-based-authentication-is-used-the-information-system-validates-certificates-by-constructing-and-verifying-a-certification-path-to-an-accepted-trust-anchor-including-checking-certificate-status-information-enforces-access-to-the-corresponding-private-key-maps-the-identity-to-the-corresponding-account-of-the-individual-or-group-and-implements-a-local-cache-of-revocation-data-to-support-path-discovery-and-validation-in-case-of-an-inability-to-access-revocation-information-via-the-network"></a>PKI tabanlı kimlik doğrulaması kullanıldığında, bilgi sistemi sertifika durum bilgilerini denetleme dahil kabul edilen bir güven çıpası için bir sertifika yolu oluşturup doğrulayarak sertifikaları doğrular; karşılık gelen özel anahtara erişimi zorlar; kimliği bireyin veya grubun ilgili hesabıyla eşler; ve, ağ üzerinden iptal bilgilerine erişememe olasılığına karşı yol bulmayı ve doğrulamayı desteklemek için bir iptal verileri yerel önbelleği uygular.

**Kimlik**: 11111.01 q2system. 4-01. q **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[MFA, aboneliğinizde okuma izinleri olan hesaplarda etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |Hesapların veya kaynakların ihlal oluşmasını engellemek için okuma ayrıcalıklarına sahip tüm abonelik hesapları için Multi-Factor Authentication (MFA) etkinleştirilmelidir. |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |

### <a name="the-information-system-employs-replay-resistant-authentication-mechanisms-such-as-nonce-one-time-passwords-or-time-stamps-to-secure-network-access-for-privileged-accounts-and-for-hardware-token-based-authentication-employs-mechanisms-that-satisfy-minimum-token-requirements-discussed-in-nist-sp-800-63-2-electronic-authentication-guideline"></a>Bilgi sistemi, ayrıcalıklı hesaplar için ağ erişiminin güvenliğini sağlamak üzere nonce, tek seferlik parolalar veya zaman damgaları gibi yeniden yürütme ile dayanıklı kimlik doğrulama mekanizmaları kullanır; donanım belirteci tabanlı kimlik doğrulaması için, NıST SP 800-63-2, elektronik kimlik doğrulama Kılavuzu ' nda açıklanan minimum belirteç gereksinimlerini karşılayan mekanizmaları kullanır.

**Kimlik**: 11112.01 q2organizational. 67-01. q **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Aboneliğiniz için en fazla 3 sahip belirtilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f11b553-d42e-4e3a-89be-32ca364cad4c) |Güvenliği aşılmış bir sahibe göre ihlal olasılığını azaltmak için 3 adede kadar abonelik sahibi belirlemeniz önerilir. |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateLessThanXOwners_Audit.json) |

### <a name="the-organization-requires-that-electronic-signatures-unique-to-one-individual-cannot-be-reused-by-or-reassigned-to-anyone-else"></a>Kuruluş, tek bir kişiye özgü olan elektronik imzaların, başka bir kişi tarafından yeniden kullanılmamasını veya yeniden atanmayacağını gerektirir.

**Kimlik**: 11208.01 q1organizational. 8-01. q **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Aboneliğinize birden fazla sahip atanmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09024ccc-0c5f-475e-9457-b7c0d9ed487b) |Yönetici erişiminin artıklığını sağlamak için birden fazla abonelik sahibi atamanız önerilir. |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateMoreThanOneOwner_Audit.json) |

### <a name="electronic-signatures-and-handwritten-signatures-executed-to-electronic-records-shall-be-linked-to-their-respective-electronic-records"></a>Elektronik kayıtlar için yürütülen elektronik imzalar ve el ile yapılan imzalar ilgili elektronik kayıtlarına bağlanır.

**Kimlik**: 11210.01 q2organizational. 10-01. q **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Yöneticiler grubunda belirtilen üyelere sahip olan Windows makinelerini denetle](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F69bf4abd-ca1e-4cf6-8b5a-762d42e61d4f) |Önkoşulların ilke atama kapsamına dağıtılmasını gerektirir. Ayrıntılar için, adresini ziyaret edin [https://aka.ms/gcpol](https://aka.ms/gcpol) . Yerel Yöneticiler grubu, ilke parametresinde listelenen bir veya daha fazla üyeyi içeriyorsa makineler uyumlu değildir. |Auditınotexists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToExclude_AINE.json) |

### <a name="signed-electronic-records-shall-contain-information-associated-with-the-signing-in-human-readable-format"></a>İmzalı elektronik kayıtlar, Kullanıcı tarafından okunabilen biçimde imzalanmayla ilişkili bilgiler içerir.

**Kimlik**: 11211.01 q2organizational. 11-01. q **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Yöneticiler grubunda belirtilen üyelerin hiçbiri eksik olan Windows makinelerini denetle](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F30f71ea1-ac77-4f26-9fc5-2d926bbd4ba7) |Önkoşulların ilke atama kapsamına dağıtılmasını gerektirir. Ayrıntılar için, adresini ziyaret edin [https://aka.ms/gcpol](https://aka.ms/gcpol) . Yerel Yöneticiler grubu, ilke parametresinde listelenen bir veya daha fazla üye içermiyorsa makineler uyumlu değildir. |Auditınotexists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToInclude_AINE.json) |

### <a name="users-who-performed-privileged-functions-eg-system-administration-use-separate-accounts-when-performing-those-privileged-functions"></a>Ayrıcalıklı işlevleri (örn. Sistem Yönetimi) gerçekleştiren kullanıcılar, bu ayrıcalıklı işlevleri gerçekleştirirken ayrı hesaplar kullanır.

**Kimlik**: 1123.01 q1system. 2-01. q **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Yöneticiler grubunda ek hesaplara sahip Windows makinelerini denetleme](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3d2a3320-2a72-4c67-ac5f-caa40fbee2b2) |Önkoşulların ilke atama kapsamına dağıtılmasını gerektirir. Ayrıntılar için, adresini ziyaret edin [https://aka.ms/gcpol](https://aka.ms/gcpol) . Yerel Yöneticiler grubu, ilke parametresinde listelenmeyen üyeler içeriyorsa, makineler uyumlu değildir. |Auditınotexists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembers_AINE.json) |

### <a name="multi-factor-authentication-methods-are-used-in-accordance-with-organizational-policy-eg-for-remote-network-access"></a>Multi-Factor Authentication yöntemleri kuruluş ilkesine uygun olarak kullanılır (örneğin, uzaktan ağ erişimi için).

**Kimlik**: 1125.01 q2system. 1-01. q **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Yöneticiler grubunda belirtilen üyelere sahip olan Windows makinelerini denetle](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F69bf4abd-ca1e-4cf6-8b5a-762d42e61d4f) |Önkoşulların ilke atama kapsamına dağıtılmasını gerektirir. Ayrıntılar için, adresini ziyaret edin [https://aka.ms/gcpol](https://aka.ms/gcpol) . Yerel Yöneticiler grubu, ilke parametresinde listelenen bir veya daha fazla üyeyi içeriyorsa makineler uyumlu değildir. |Auditınotexists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToExclude_AINE.json) |

### <a name="where-tokens-are-provided-for-multi-factor-authentication-in-person-verification-is-required-prior-to-granting-access"></a>Çok faktörlü kimlik doğrulaması için belirteçlerin sağlandığı durumlar için, erişim izni vermeden önce kişi içi doğrulama gerekir.

**Kimlik**: 1127.01 q2system. 3-01. q **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Yöneticiler grubunda belirtilen üyelerin hiçbiri eksik olan Windows makinelerini denetle](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F30f71ea1-ac77-4f26-9fc5-2d926bbd4ba7) |Önkoşulların ilke atama kapsamına dağıtılmasını gerektirir. Ayrıntılar için, adresini ziyaret edin [https://aka.ms/gcpol](https://aka.ms/gcpol) . Yerel Yöneticiler grubu, ilke parametresinde listelenen bir veya daha fazla üye içermiyorsa makineler uyumlu değildir. |Auditınotexists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToInclude_AINE.json) |

## <a name="identification-of-risks-related-to-external-parties"></a>Dış taraflarla Ilgili risklerin tanımlanması

### <a name="access-to-the-organizations-information-and-systems-by-external-parties-is-not-permitted-until-due-diligence-has-been-conducted-the-appropriate-controls-have-been-implemented-and-a-contractagreement-reflecting-the-security-requirements-is-signed-acknowledging-they-understand-and-accept-their-obligations"></a>Kuruluş bilgilerine ve sistemlerine dış taraflara göre erişime izin verilmez, çünkü bu, uygun denetimler uygulandı ve güvenlik gereksinimlerini yansıtan bir sözleşme/anlaşma, bu bilgileri anladıkları ve kabul ettikleri bir sözleşme/sözleşme ile ilgili yükümlülüklere neden olur.

**Kimlik**: 1401.05 i1organizational. 1239-05. i **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Depolama hesaplarına güvenli aktarım etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |Depolama hesabınızda güvenli aktarım gereksinimini denetleyin. Güvenli aktarım, depolama hesabınızı yalnızca güvenli bağlantılardan (HTTPS) istekleri kabul edecek şekilde zorlayan bir seçenektir. HTTPS kullanımı, sunucu ile hizmet arasında kimlik doğrulaması sağlar ve geçiş sırasında ortadaki adam, gizlice dinleme ve oturum ele geçirme gibi ağ katmanı saldırılarına karşı verileri korur |Denetim, reddetme, devre dışı |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |

### <a name="remote-access-connections-between-the-organization-and-external-parties-are-encrypted"></a>Kuruluş ve dış taraflar arasındaki uzaktan erişim bağlantıları şifrelenir.

**Kimlik**: 1402.05 i1organizational. 45-05. ben **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[İşlev Uygulaması yalnızca HTTPS üzerinden erişilebilir olmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |HTTPS kullanımı, sunucu/hizmet kimlik doğrulamasını sağlar ve ağ katmanı gizlice dinleme saldırılarına karşı geçiş sırasında verileri korur. |Denetim, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |

### <a name="access-granted-to-external-parties-is-limited-to-the-minimum-necessary-and-granted-only-for-the-duration-required"></a>Dış taraflara verilen erişim, gerekli olan en düşük ve yalnızca gereken süre için izin verilen en az ile sınırlıdır.

**Kimlik**: 1403.05 i1organizational. 67-05. ben **sahipim**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Web uygulaması yalnızca HTTPS üzerinden erişilebilir olmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |HTTPS kullanımı, sunucu/hizmet kimlik doğrulamasını sağlar ve ağ katmanı gizlice dinleme saldırılarına karşı geçiş sırasında verileri korur. |Denetim, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

### <a name="due-diligence-of-the-external-party-includes-interviews-document-review-checklists-certification-reviews-eg-hitrustnbspor-other-remote-means"></a>Dış tarafın bir anlamı, görüşmeleri, belge incelemeyi, denetim listelerini, sertifika incelemelerini (örn. HITRUST) &nbsp; veya başka uzak yollarla içerir.

**Kimlik**: 1404.05 i2organizational. 1-05. ben **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[API uygulaması yalnızca HTTPS üzerinden erişilebilir olmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |HTTPS kullanımı, sunucu/hizmet kimlik doğrulamasını sağlar ve ağ katmanı gizlice dinleme saldırılarına karşı geçiş sırasında verileri korur. |Denetim, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |

### <a name="the-identification-of-risks-related-to-external-party-access-takes-into-account-a-minimal-set-of-specifically-defined-issues"></a>Dış taraf erişimiyle ilgili risklerin tanımlanması, özellikle tanımlanmış olan en az bir sorun kümesini dikkate alır.

**Kimlik**: 1418.05 i1organizational. 8-05. ben **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[MySQL veritabanı sunucuları için SSL bağlantısını zorla etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |MySQL için Azure veritabanı, MySQL Server için Azure veritabanı sunucunuzu Güvenli Yuva Katmanı (SSL) kullanarak istemci uygulamalarına bağlamayı destekler. Veritabanı sunucunuz ile istemci uygulamalarınız arasında SSL bağlantılarını zorunlu tutma, sunucu ile uygulamanız arasındaki veri akışını şifreleyerek, ortadaki adam saldırılarına karşı korunmaya yardımcı olur. Bu yapılandırma, veritabanı sunucunuza erişmek için SSL 'nin her zaman etkinleştirilmesini zorunlu kılar. |Denetim, devre dışı |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |

### <a name="the-organization-obtains-satisfactory-assurances-that-reasonable-information-security-exists-across-their-information-supply-chain-by-performing-an-annual-review-which-includes-all-partnersthird-party-providers-upon-which-their-information-supply-chain-depends"></a>Kuruluş, bilgi tedarik zincirinin bağımlı olduğu tüm iş ortakları/üçüncü taraf sağlayıcıları dahil olmak üzere yıllık bir gözden geçirme gerçekleştirerek, bilgi tedarik zinciri genelinde makul bilgi güvenliğini elde eden tatmin edici bir şekilde alır.

**Kimlik**: 1450.05 i2organizational. 2-05. ben **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[PostgreSQL veritabanı sunucuları için SSL bağlantısını zorla etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |PostgreSQL için Azure veritabanı, PostgreSQL için Azure veritabanı sunucunuzu Güvenli Yuva Katmanı (SSL) kullanarak istemci uygulamalarına bağlamayı destekler. Veritabanı sunucunuz ile istemci uygulamalarınız arasında SSL bağlantılarını zorunlu tutma, sunucu ile uygulamanız arasındaki veri akışını şifreleyerek, ortadaki adam saldırılarına karşı korunmaya yardımcı olur. Bu yapılandırma, veritabanı sunucunuza erişmek için SSL 'nin her zaman etkinleştirilmesini zorunlu kılar. |Denetim, devre dışı |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |

### <a name="cloud-service-providers-design-and-implement-controls-to-mitigate-and-contain-data-security-risks-through-proper-separation-of-duties-role-based-access-and-least-privilege-access-for-all-personnel-within-their-supply-chain"></a>Bulut hizmeti sağlayıcıları, görevlerin uygun ayrımı, rol tabanlı erişim ve tedarik zincirindeki tüm personel için en az ayrıcalık erişimi aracılığıyla veri güvenliği risklerini azaltmak ve içermesi için denetimleri tasarlar ve uygular.

**Kimlik**: 1451.05 Icspkuruluş. 2-05. ben **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Redsıs için yalnızca Azure önbelleğinize güvenli bağlantılar etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Redsıs için yalnızca SSL ile SSL aracılığıyla bağlantıların etkinleştirilmesini denetleme. Güvenli bağlantı kullanımı, sunucu ve hizmet arasında kimlik doğrulaması sağlar ve geçiş sırasında ortadaki adam, gizlice dinleme ve oturum ele geçirme gibi ağ katmanı saldırılarına karşı verileri korur |Denetim, reddetme, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

## <a name="audit-logging"></a>Denetim günlüğü

### <a name="a-secure-audit-record-is-created-for-all-activities-on-the-system-create-read-update-delete-involving-covered-information"></a>Kapsanan bilgileri içeren sistemdeki tüm etkinlikler (oluşturma, okuma, güncelleştirme, silme) için güvenli bir denetim kaydı oluşturulur.

**Kimlik**: 1202.09 aa1system. 1-09. aa **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Azure Data Lake Store kaynak günlüklerinin etkinleştirilmesi gerekir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F057ef27e-665e-4328-8ea3-04b3122bd9fb) |Kaynak günlüklerinin etkinleştirilmesi için denetim. Bu, araştırma amacıyla kullanılacak etkinlik izlerini yeniden oluşturmayı sağlar; bir güvenlik olayı gerçekleştiğinde veya ağınız tehlikede olduğunda |Auditınotexists, devre dışı |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeStore_AuditDiagnosticLog_Audit.json) |
|[Sanal makine ölçek kümelerindeki sistem güncelleştirmeleri yüklenmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc3f317a7-a95c-4547-b7e7-11017ebdf2fe) |Windows ve Linux sanal makine ölçek kümelerinizin güvende olduğundan emin olmak için yüklenmesi gereken eksik sistem güvenlik güncelleştirmelerinin ve kritik güncelleştirmelerin olup olmadığını denetleyin. |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssMissingSystemUpdates_Audit.json) |

### <a name="audit-records-include-the-unique-user-id-unique-data-subject-id-function-performed-and-datetime-the-event-was-performed"></a>Denetim kayıtları benzersiz kullanıcı KIMLIĞI, benzersiz veri konu KIMLIĞI, gerçekleştirilen işlev ve etkinliğin gerçekleştirildiği tarih/saat ' i içerir.

**Kimlik**: 1203.09 aa1system. 2-09. aa **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Logic Apps kaynak günlüklerinin etkinleştirilmesi gerekir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34f95f76-5386-4de7-b824-0d8478470c9d) |Kaynak günlüklerinin etkinleştirilmesi için denetim. Bu, araştırma amacıyla kullanılacak etkinlik izlerini yeniden oluşturmayı sağlar; bir güvenlik olayı gerçekleştiğinde veya ağınız tehlikede olduğunda |Auditınotexists, devre dışı |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Logic%20Apps/LogicApps_AuditDiagnosticLog_Audit.json) |

### <a name="the-activities-of-privileged-users-administrators-operators-etc-include-the-successfailure-of-the-event-time-the-event-occurred-the-account-involved-the-processes-involved-and-additional-information-about-the-event"></a>Ayrıcalıklı kullanıcıların (Yöneticiler, işleçler, vb.) etkinlikleri, olayın başarısını/başarısızlığını, olayın gerçekleştiği süreyi, dahil edilen işlemleri ve olayla ilgili ek bilgileri içerir.

**Kimlik**: 1204.09 aa1system. 3-09. aa **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[IoT Hub kaynak günlüklerinin etkinleştirilmesi gerekir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F383856f8-de7f-44a2-81fc-e5135b5c2aa4) |Kaynak günlüklerinin etkinleştirilmesi için denetim. Bu, araştırma amacıyla kullanılacak etkinlik izlerini yeniden oluşturmayı sağlar; bir güvenlik olayı gerçekleştiğinde veya ağınız tehlikede olduğunda |Auditınotexists, devre dışı |[3.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTHub_AuditDiagnosticLog_Audit.json) |

### <a name="logs-of-messages-sent-and-received-are-maintained-including-the-date-time-origin-and-destination-of-the-message-but-not-its-contents"></a>Gönderilen ve alınan ileti günlükleri, iletinin tarihi, saati, kaynağı ve hedefi de dahil olmak üzere saklanır, ancak içeriğini değil.

**Kimlik**: 1205.09 aa2system. 1-09. aa **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Batch hesaplarındaki kaynak günlüklerinin etkinleştirilmesi gerekir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F428256e6-1fac-4f48-a757-df34c2b3336d) |Kaynak günlüklerinin etkinleştirilmesi için denetim. Bu, araştırma amacıyla kullanılacak etkinlik izlerini yeniden oluşturmayı sağlar; bir güvenlik olayı gerçekleştiğinde veya ağınız tehlikede olduğunda |Auditınotexists, devre dışı |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Batch/Batch_AuditDiagnosticLog_Audit.json) |

### <a name="auditing-is-always-available-while-the-system-is-active-and-tracks-key-events-successfailed-data-access-system-security-configuration-changes-privileged-or-utility-use-any-alarms-raised-nbspactivation-and-de-activation-of-protection-systems-eg-av-and-ids-activation-and-deactivation-of-identification-and-authentication-mechanisms-and-creation-and-deletion-of-system-level-objects"></a>Sistem etkin durumdayken denetim her zaman kullanılabilir ve önemli olayları, başarı/başarısız veri erişimi, sistem güvenlik yapılandırması değişiklikleri, ayrıcalıklı veya yardımcı program kullanımı, tüm alarmlar &nbsp; (örneğin, A/V ve kimlikler), kimlik ve kimlik doğrulama mekanizmalarının etkinleştirilmesi ve devre dışı bırakılması, sistem düzeyindeki nesnelerin oluşturulması ve silinmesi.

**Kimlik**: 1206.09 aa2system. 23-09. aa **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Sanal makine ölçek kümelerindeki kaynak günlükleri etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7c1b1214-f927-48bf-8882-84f0af6588b1) |Bir olay veya bir uzlaşmaya karşı araştırma gerektiğinde etkinlik izinin yeniden oluşturulması için günlüklerin etkinleştirilmesi önerilir. |Auditınotexists, devre dışı |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/ServiceFabric_and_VMSS_AuditVMSSDiagnostics.json) |

### <a name="audit-records-are-retained-for-90-days-and-older-audit-records-are-archived-for-one-year"></a>Denetim kayıtları 90 gün boyunca tutulur ve daha eski denetim kayıtları bir yıl boyunca arşivlenir.

**Kimlik**: 1207.09 aa2system. 4-09. aa **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Azure Stream Analytics kaynak günlüklerinin etkinleştirilmesi gerekir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9be5368-9bf5-4b84-9e0a-7850da98bb46) |Kaynak günlüklerinin etkinleştirilmesi için denetim. Bu, araştırma amacıyla kullanılacak etkinlik izlerini yeniden oluşturmayı sağlar; bir güvenlik olayı gerçekleştiğinde veya ağınız tehlikede olduğunda |Auditınotexists, devre dışı |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Stream%20Analytics/StreamAnalytics_AuditDiagnosticLog_Audit.json) |
|[Olay Hub 'ındaki kaynak günlükleri etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F83a214f7-d01a-484b-91a9-ed54470c9a6a) |Kaynak günlüklerinin etkinleştirilmesi için denetim. Bu, araştırma amacıyla kullanılacak etkinlik izlerini yeniden oluşturmayı sağlar; bir güvenlik olayı gerçekleştiğinde veya ağınız tehlikede olduğunda |Auditınotexists, devre dışı |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_AuditDiagnosticLog_Audit.json) |

### <a name="audit-logs-are-maintained-for-management-activities-system-and-application-startupshutdownerrors-file-changes-and-security-policy-changes"></a>Denetim günlükleri, yönetim etkinlikleri, sistem ve uygulama başlatma/kapatmada/hatalarda, dosya değişikliklerinde ve güvenlik ilkesi değişikliklerinde korunur.

**Kimlik**: 1208.09 aa3system. 1-09. aa **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Arama hizmetlerindeki kaynak günlüklerinin etkinleştirilmesi gerekir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4330a05-a843-4bc8-bf9a-cacce50c67f4) |Kaynak günlüklerinin etkinleştirilmesi için denetim. Bu, araştırma amacıyla kullanılacak etkinlik izlerini yeniden oluşturmayı sağlar; bir güvenlik olayı gerçekleştiğinde veya ağınız tehlikede olduğunda |Auditınotexists, devre dışı |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Search/Search_AuditDiagnosticLog_Audit.json) |
|[Service Bus kaynak günlüklerinin etkinleştirilmesi gerekir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8d36e2f-389b-4ee4-898d-21aeb69a0f45) |Kaynak günlüklerinin etkinleştirilmesi için denetim. Bu, araştırma amacıyla kullanılacak etkinlik izlerini yeniden oluşturmayı sağlar; bir güvenlik olayı gerçekleştiğinde veya ağınız tehlikede olduğunda |Auditınotexists, devre dışı |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditDiagnosticLog_Audit.json) |

### <a name="the-information-system-generates-audit-records-containing-the-following-detailed-information-i-filename-accessed-ii-program-or-command-used-to-initiate-the-event-and-iii-source-and-destination-addresses"></a>Bilgi sistemi, şu ayrıntılı bilgileri içeren denetim kayıtları oluşturur: (i) dosya adı erişildi; (II) olayı başlatmak için kullanılan program veya komut; ve (iii) kaynak ve hedef adresler.

**Kimlik**: 1209.09 aa3system. 2-09. aa **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Uygulama hizmetlerindeki tanılama günlükleri etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb607c5de-e7d9-4eee-9e5c-83f1bcee4fa0) |Uygulamada tanılama günlüklerinin etkinleştirilmesini denetleme. Bu, bir güvenlik olayı gerçekleşirse veya ağınızın güvenliği tehlikeye girerse araştırma amacıyla etkinlik izlerini yeniden oluşturmayı sağlar |Auditınotexists, devre dışı |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_AuditLoggingMonitoring_Audit.json) |

### <a name="all-disclosures-of-covered-information-within-or-outside-of-the-organization-are-logged-including-type-of-disclosure-datetime-of-the-event-recipient-and-sender"></a>Kuruluşun içinde veya dışında kapsanan bilgilerin tüm kapanışları, olay, alıcı ve gönderici için açıklama türü, tarih/saat gibi günlüğe kaydedilir.

**Kimlik**: 1210.09 aa3system. 3-09. aa **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Tanılama ayarını denetle](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7f89b1eb-583c-429a-8828-af049802c1d9) |Seçili kaynak türleri için tanılama ayarını denetle |AuditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/DiagnosticSettingsForTypes_Audit.json) |
|[Data Lake Analytics kaynak günlüklerinin etkinleştirilmesi gerekir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc95c74d9-38fe-4f0d-af86-0c7d626a315c) |Kaynak günlüklerinin etkinleştirilmesi için denetim. Bu, araştırma amacıyla kullanılacak etkinlik izlerini yeniden oluşturmayı sağlar; bir güvenlik olayı gerçekleştiğinde veya ağınız tehlikede olduğunda |Auditınotexists, devre dışı |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeAnalytics_AuditDiagnosticLog_Audit.json) |

### <a name="the-organization-verifies-every-ninety-90-days-for-each-extract-of-covered-information-recorded-that-the-data-is-erased-or-its-use-is-still-required"></a>Kuruluş, verilerin silindiğini veya kullanımı hala gerekli olan tüm kapsanan bilgileri ayıklayarak her 90 (90) günde bir doğrular.

**Kimlik**: 1211.09 aa3system. 4-09. aa **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[SQL Server üzerinde denetim etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa6fb4358-5bf4-4ad7-ba82-2cd2f41ce5e9) |SQL Server denetim, sunucudaki tüm veritabanları genelinde veritabanı etkinliklerini izlemek ve bir denetim günlüğüne kaydetmek için etkinleştirilmelidir. |Auditınotexists, devre dışı |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditing_Audit.json) |
|[Azure Key Vault yönetilen HSM 'deki kaynak günlükleri etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa2a5b911-5617-447e-a49e-59dbe0e0434b) |Bir güvenlik olayı gerçekleştiğinde veya ağınızın güvenliği ihlal edildiğinde araştırma amacıyla etkinlik izlerini yeniden oluşturmak için, yönetilen HSM 'lerde kaynak günlüklerini etkinleştirerek denetlemek isteyebilirsiniz. Lütfen buradaki yönergeleri izleyin: [https://docs.microsoft.com/azure/key-vault/managed-hsm/logging](https://docs.microsoft.com/azure/key-vault/managed-hsm/logging) . |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/ManagedHsm_AuditDiagnosticLog_Audit.json) |
|[Key Vault kaynak günlüklerinin etkinleştirilmesi gerekir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf820ca0-f99e-4f3e-84fb-66e913812d21) |Kaynak günlüklerinin etkinleştirilmesi için denetim. Bu, bir güvenlik olayı gerçekleştiğinde veya ağınızın güvenliği tehlikeye düştüğünde araştırma amaçlarıyla kullanılacak etkinlik izlerini yeniden oluşturmayı sağlar |Auditınotexists, devre dışı |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_AuditDiagnosticLog_Audit.json) |

## <a name="monitoring-system-use"></a>İzleme sistem kullanımı

### <a name="unauthorized-remote-connections-to-the-information-systems-are-monitored-and-reviewed-at-least-quarterly-and-appropriate-action-is-taken-if-an-unauthorized-connection-is-discovered"></a>Bilgi sistemlerine yönelik yetkisiz uzak bağlantılar, en az üç ayda bir izlenir ve incelenir ve yetkisiz bir bağlantı bulunursa uygun bir işlem gerçekleştirilir.

**Kimlik**: 1120.09 ab3system. 9-09. AB **sahipliği**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Azure Izleyici, tüm bölgelerdeki etkinlik günlüklerini toplamalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F41388f1c-2db0-4c25-95b2-35d7f5ccbfa9) |Bu ilke, genel dahil olmak üzere tüm Azure desteklenen bölgelerden etkinlikleri dışarı aktarmayan Azure Izleyici günlük profilini denetler. |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllRegions.json) |

### <a name="the-organization-monitors-the-information-system-to-identify-irregularities-or-anomalies-that-are-indicators-of-a-system-malfunction-or-compromise-and-help-confirm-the-system-is-functioning-in-an-optimal-resilient-and-secure-state"></a>Kuruluş, sistem arızası veya güvenliğinin aşılmasına neden olan ve sistemin en iyi, dayanıklı ve güvenli bir durumda çalıştığını onaylamaya yardımcı olan ırdüzenlenlikleri veya bozuklukları belirlemek için bilgi sistemini izler.

**Kimlik**: 12100.09 ab2system. 15-09. AB **sahipliği**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Log Analytics Aracısı sanal makinelere yüklenmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa70ca396-0a34-413a-88e1-b956c1e683be) |Log Analytics Aracısı yüklenmemişse, bu ilke tüm Windows/Linux sanal makinelerini denetler. |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/VirtualMachines_LogAnalyticsAgent_AuditIfNotExists.json) |

### <a name="the-organization-specifies-how-often-audit-logs-are-reviewed-how-the-reviews-are-documented-and-the-specific-roles-and-responsibilities-of-the-personnel-conducting-the-reviews-including-the-professional-certifications-or-other-qualifications-required"></a>Kuruluş, denetim günlüklerinin ne sıklıkla incelendiğini, incelemelerin nasıl belgelendiğini ve uzman sertifikaları ya da diğer nitelikleri de kapsayan, İncelemeleri üstlenen personelin belirli rollerini ve sorumluluklarını belirler.

**Kimlik**: 12101.09 ab1organizational. 3-09. AB **sahipliği**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Log Analytics Aracısı sanal makine ölçek kümelerine yüklenmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fefbde977-ba53-4479-b8e9-10b957924fbf) |Log Analytics Aracısı yüklenmemişse, bu ilke tüm Windows/Linux sanal makine ölçek kümelerini denetler. |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/VMSS_LogAnalyticsAgent_AuditIfNotExists.json) |

### <a name="the-organization-shall-periodically-test-its-monitoring-and-detection-processes-remediate-deficiencies-and-improve-its-processes"></a>Kuruluş, izleme ve algılama süreçlerini düzenli olarak test etme, eksiklik ve işlem sürecini geliştirme sürecine sahip olacaktır.

**Kimlik**: 12102.09 ab1organizational. 4-09. AB **sahipliği**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Log Analytics aracısının beklendiği gibi bağlı olmadığı Windows makinelerini denetle](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6265018c-d7e2-432f-a75d-094d5f6f4465) |Önkoşulların ilke atama kapsamına dağıtılmasını gerektirir. Ayrıntılar için, adresini ziyaret edin [https://aka.ms/gcpol](https://aka.ms/gcpol) . Aracı yüklü değilse veya yüklüyse ancak AgentConfigManager. MgmtSvcCfg COM nesnesi, ilke parametresinde belirtilen KIMLIğIN dışında bir çalışma alanına kayıtlı olduğunu döndürürse, makineler uyumlu değildir. |Auditınotexists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsLogAnalyticsAgentConnection_AINE.json) |

### <a name="all-applicable-legal-requirements-related-to-monitoring-authorized-access-and-unauthorized-access-attempts-are-met"></a>Yetkili erişimi ve yetkisiz erişim girişimlerini izlemeyle ilgili tüm geçerli gereksinimler karşılanır.

**Kimlik**: 1212.09 ab1system. 1-09. AB **sahipliği**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Azure Izleyici günlük profili, ' Write, ' ' DELETE, ' ve ' Action ' kategorilerinin günlüklerini toplamalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1a4e592a-6a6e-44a5-9814-e36264ca96e7) |Bu ilke, bir günlük profilinin ' Write, ' ' DELETE, ' ve ' Action ' kategorilerinin günlüklerini toplamasına sağlar |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllCategories.json) |

### <a name="automated-systems-deployed-throughout-the-organizations-environment-are-used-to-monitor-key-events-and-anomalous-activity-and-analyze-system-logs-the-results-of-which-are-reviewed-regularly"></a>Kuruluşun ortamında dağıtılan otomatikleştirilmiş sistemler, önemli olayları ve anormal etkinlikleri izlemek ve sistem günlüklerini analiz etmek için kullanılır. bunun sonuçları düzenli olarak gözden geçirilir.

**Kimlik**: 1213.09 ab2system. 128-09. AB **sahipliği**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Log Analytics aracısının otomatik olarak sağlanması aboneliğinizde etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F475aae12-b88a-4572-8b36-9b712b2b3a17) |Azure Güvenlik Merkezi, güvenlik açıklarını ve tehditleri izlemek için Azure sanal makinelerinizden veri toplar. Veriler, makineden güvenlikle ilgili çeşitli yapılandırma ve olay günlüklerini okuyan ve verileri analiz için Log Analytics çalışma alanınıza kopyalayan, daha önce Microsoft Monitoring Agent (MMA) olarak bilinen Log Analytics Aracısı tarafından toplanır. Otomatik sağlamanın, aracıyı desteklenen tüm Azure VM 'lerine ve oluşturulan tüm yeni makinelere otomatik olarak dağıtması için etkinleştirmenizi öneririz. |Auditınotexists, devre dışı |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Automatic_provisioning_log_analytics_monitoring_agent.json) |

### <a name="monitoring-includes-privileged-operations-authorized-access-or-unauthorized-access-attempts-including-attempts-to-access-deactivated-accounts-and-system-alerts-or-failures"></a>İzleme, devre dışı bırakılmış hesaplara erişim girişimleri ve sistem uyarıları ya da arızalar dahil ayrıcalıklı işlemler, yetkili erişim veya yetkisiz erişim girişimleri içerir.

**Kimlik**: 1214.09 ab2system. 3456-09. AB **sahipliği**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Azure Izleyici, tüm bölgelerdeki etkinlik günlüklerini toplamalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F41388f1c-2db0-4c25-95b2-35d7f5ccbfa9) |Bu ilke, genel dahil olmak üzere tüm Azure desteklenen bölgelerden etkinlikleri dışarı aktarmayan Azure Izleyici günlük profilini denetler. |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllRegions.json) |

### <a name="auditing-and-monitoring-systems-employed-by-the-organization-support-audit-reduction-and-report-generation"></a>Kuruluş tarafından çalıştırılan denetim ve izleme sistemleri denetim azaltma ve rapor oluşturmayı destekler.

**Kimlik**: 1215.09 ab2system. 7-09. AB **sahipliği**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Log Analytics Aracısı sanal makinelere yüklenmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa70ca396-0a34-413a-88e1-b956c1e683be) |Log Analytics Aracısı yüklenmemişse, bu ilke tüm Windows/Linux sanal makinelerini denetler. |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/VirtualMachines_LogAnalyticsAgent_AuditIfNotExists.json) |

### <a name="automated-systems-are-used-to-review-monitoring-activities-of-security-systems-eg-ipsids-and-system-records-on-a-daily-basis-and-identify-and-document-anomalies"></a>Otomatik sistemler, güvenlik sistemlerinin (örn. IP 'ler/KIMLIKLER) ve sistem kayıtlarının izleme etkinliklerini günlük olarak gözden geçirmek için kullanılır ve anormallikleri belirleyip belgeleyin.

**Kimlik**: 1216.09 ab3system. 12-09. AB **sahipliği**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Log Analytics Aracısı sanal makine ölçek kümelerine yüklenmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fefbde977-ba53-4479-b8e9-10b957924fbf) |Log Analytics Aracısı yüklenmemişse, bu ilke tüm Windows/Linux sanal makine ölçek kümelerini denetler. |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/VMSS_LogAnalyticsAgent_AuditIfNotExists.json) |

### <a name="alerts-are-generated-for-technical-personnel-to-analyze-and-investigate-suspicious-activity-or-suspected-violations"></a>Teknik personel için uyarılar, şüpheli etkinliği veya şüpheli ihlalleri çözümlemek ve araştırmak için oluşturulur.

**Kimlik**: 1217.09 ab3system. 3-09. AB **sahipliği**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Log Analytics aracısının beklendiği gibi bağlı olmadığı Windows makinelerini denetle](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6265018c-d7e2-432f-a75d-094d5f6f4465) |Önkoşulların ilke atama kapsamına dağıtılmasını gerektirir. Ayrıntılar için, adresini ziyaret edin [https://aka.ms/gcpol](https://aka.ms/gcpol) . Aracı yüklü değilse veya yüklüyse ancak AgentConfigManager. MgmtSvcCfg COM nesnesi, ilke parametresinde belirtilen KIMLIğIN dışında bir çalışma alanına kayıtlı olduğunu döndürürse, makineler uyumlu değildir. |Auditınotexists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsLogAnalyticsAgentConnection_AINE.json) |

### <a name="the-information-system-is-able-to-automatically-process-audit-records-for-events-of-interest-based-on-selectable-criteria"></a>Bilgi sistemi, seçilebilir ölçütlere göre ilgilendiğiniz olaylar için denetim kayıtlarını otomatik olarak işleyebilir.

**Kimlik**: 1219.09 ab3system. 10-09. AB **sahipliği**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Azure Izleyici günlük profili, ' Write, ' ' DELETE, ' ve ' Action ' kategorilerinin günlüklerini toplamalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1a4e592a-6a6e-44a5-9814-e36264ca96e7) |Bu ilke, bir günlük profilinin ' Write, ' ' DELETE, ' ve ' Action ' kategorilerinin günlüklerini toplamasına sağlar |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllCategories.json) |

### <a name="monitoring-includes-inbound-and-outbound-communications-and-file-integrity-monitoring"></a>İzleme, gelen ve giden iletişimleri ve dosya bütünlüğü izlemeyi içerir.

**Kimlik**: 1220.09 ab3system. 56-09. AB **sahipliği**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Log Analytics aracısının otomatik olarak sağlanması aboneliğinizde etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F475aae12-b88a-4572-8b36-9b712b2b3a17) |Azure Güvenlik Merkezi, güvenlik açıklarını ve tehditleri izlemek için Azure sanal makinelerinizden veri toplar. Veriler, makineden güvenlikle ilgili çeşitli yapılandırma ve olay günlüklerini okuyan ve verileri analiz için Log Analytics çalışma alanınıza kopyalayan, daha önce Microsoft Monitoring Agent (MMA) olarak bilinen Log Analytics Aracısı tarafından toplanır. Otomatik sağlamanın, aracıyı desteklenen tüm Azure VM 'lerine ve oluşturulan tüm yeni makinelere otomatik olarak dağıtması için etkinleştirmenizi öneririz. |Auditınotexists, devre dışı |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Automatic_provisioning_log_analytics_monitoring_agent.json) |

## <a name="administrator-and-operator-logs"></a>Yönetici ve operatör günlükleri

### <a name="the-organization-ensures-proper-logging-is-enabled-in-order-to-audit-administrator-activities-and-reviews-system-administrator-and-operator-logs-on-a-regular-basis"></a>Kuruluş, yönetici etkinliklerini denetlemek için doğru günlük kaydının etkinleştirilmesini sağlar; ve sistem yöneticisi ve işletmen günlüklerini düzenli olarak inceler.

**Kimlik**: 1270.09 ad1system. 12-09.ad **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Belirli yönetim işlemleri için bir etkinlik günlüğü uyarısı bulunmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Bu ilke, hiçbir etkinlik günlüğü uyarısı yapılandırılmadığı belirli yönetim işlemlerini denetler. |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="an-intrusion-detection-system-managed-outside-of-the-control-of-system-and-network-administrators-is-used-to-monitor-system-and-network-administration-activities-for-compliance"></a>Sistem ve ağ yöneticileri denetimi dışında yönetilen bir yetkisiz giriş algılama sistemi, uyumluluk için sistem ve ağ yönetim etkinliklerini izlemek için kullanılır.

**Kimlik**: 1271.09 ad1system. 1-09.ad **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Belirli yönetim işlemleri için bir etkinlik günlüğü uyarısı bulunmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Bu ilke, hiçbir etkinlik günlüğü uyarısı yapılandırılmadığı belirli yönetim işlemlerini denetler. |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

## <a name="segregation-of-duties"></a>Görevlerin ayrımı

### <a name="separation-of-duties-is-used-to-limit-the-risk-of-unauthorized-or-unintentional-modification-of-information-and-systems"></a>Görevlerin ayrımı, bilgi ve sistemlerin yetkisiz veya istenmeden değiştirilmesi riskini sınırlamak için kullanılır.

**Kimlik**: 1229.09 c1organizational. 1-09. c **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Rol tabanlı Access Control (RBAC), Kubernetes hizmetlerinde kullanılmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |Kullanıcıların gerçekleştirebileceği eylemlere ayrıntılı filtreleme sağlamak için, Kubernetes hizmet kümelerindeki izinleri yönetmek ve ilgili yetkilendirme ilkelerini yapılandırmak üzere Role-Based Access Control (RBAC) kullanın. |Denetim, devre dışı |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |

### <a name="no-single-person-is-able-to-access-modify-or-use-information-systems-without-authorization-or-detection"></a>Tek bir kişi yetkilendirme veya algılama olmadan bilgi sistemlerine erişemez, bunları değiştirebilir veya kullanamaz.

**Kimlik**: 1230.09 c2organizational. 1-09. c **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Özel RBAC kurallarının kullanımını denetleme](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa451c1ef-c6ca-483d-87ed-f49761e3ffb5) |Hataya açık olan özel RBAC rolleri yerine ' Owner, katılımcısı, Reader ' gibi yerleşik rolleri denetleyin. Özel rolleri kullanmak özel durum olarak değerlendirilir ve kapsamlı bir inceleme ve tehdit modelleme gerektirir |Denetim, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |

### <a name="access-for-individuals-responsible-for-administering-nbspaccess-controls-is-limited-to-the-minimum-necessary-based-upon-each-users-role-and-responsibilities-and-these-individuals-cannot-access-audit-functions-related-to-these-controls"></a>Erişim denetimlerini yönetmekten sorumlu kişiler için erişim &nbsp; , her kullanıcının rolüne ve sorumluluklarına göre gerekli olan en düşük gerekliliktir ve bu kişiler, bu denetimlerle ilgili denetim işlevlerine erişemez.

**Kimlik**: 1232.09 c3organizational. 12-09. c **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Windows makineleri ' Kullanıcı hakları ataması ' gereksinimlerini karşılamalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe068b215-0026-4354-b347-8fb2766f73a2) |Windows makineler, yerel olarak oturum açma, RDP, ağ erişimi ve diğer birçok kullanıcı etkinliği için ' Kullanıcı hakları ataması ' kategorisinde belirtilen grup ilkesi ayarlara sahip olmalıdır. Bu ilke, Konuk yapılandırma ön koşullarının ilke atama kapsamına dağıtılmasını gerektirir. Ayrıntılar için, adresini ziyaret edin [https://aka.ms/gcpol](https://aka.ms/gcpol) . |Auditınotexists, devre dışı |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_UserRightsAssignment_AINE.json) |

### <a name="security-audit-activities-are-independent"></a>Güvenlik denetimi etkinlikleri bağımsızdır.

**Kimlik**: 1276.09 c2organizational. 2-09. c **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Özel abonelik sahibi rolleri mevcut olmamalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F10ee2ea2-fb4d-45b8-a7e9-a2e770044cd9) |Bu ilke, özel abonelik sahibi rolü olmamasını sağlar. |Denetim, devre dışı |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json) |

### <a name="the-initiation-of-an-event-is-separated-from-its-authorization-to-reduce-the-possibility-of-collusion"></a>Bir olayın başlatılması, bir olay başlatma olasılığını azaltmak için yetkilendirmeden ayrılır.

**Kimlik**: 1277.09 c2organizational. 4-09. c **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Windows makineleri, ' güvenlik seçenekleri-Kullanıcı hesabı denetimi ' gereksinimlerini karşılamalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F492a29ed-d143-4f03-b6a4-705ce081b463) |Windows makineleri, ' güvenlik seçenekleri-Kullanıcı hesabı denetimi ' kategorisinde Yöneticiler için mod, yükseltme isteminin davranışı ve dosya ve kayıt defteri yazma başarısızlıklarını sanallaştırarak belirtilen grup ilkesi ayarlara sahip olmalıdır. Bu ilke, Konuk yapılandırma ön koşullarının ilke atama kapsamına dağıtılmasını gerektirir. Ayrıntılar için, adresini ziyaret edin [https://aka.ms/gcpol](https://aka.ms/gcpol) . |Auditınotexists, devre dışı |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecurityOptionsUserAccountControl_AINE.json) |

### <a name="the-organization-identifies-duties-that-require-separation-and-defines-information-system-access-authorizations-to-support-separation-of-duties-and-incompatible-duties-are-segregated-across-multiple-users-to-minimize-the-opportunity-for-misuse-or-fraud"></a>Kuruluş, ayırmayı gerektiren görevleri tanımlar ve görevlerin ayrılmasını desteklemek için bilgi sistemi erişim yetkilendirmelerini tanımlar; ve uyumsuz görevler, kötü amaçlı veya sahtekarlık fırsatını en aza indirmek için birden fazla kullanıcı arasında ayrım yapılır.

**Kimlik**: 1278.09 c2organizational. 56-09. c **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Özel abonelik sahibi rolleri mevcut olmamalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F10ee2ea2-fb4d-45b8-a7e9-a2e770044cd9) |Bu ilke, özel abonelik sahibi rolü olmamasını sağlar. |Denetim, devre dışı |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json) |

## <a name="controls-against-malicious-code"></a>Kötü amaçlı koda karşı denetimler

### <a name="anti-virus-and-anti-spyware-are-installed-operating-and-updated-on-all-end-user-devices-to-conduct-periodic-scans-of-the-systems-to-identify-and-remove-unauthorized-software-server-environments-for-which-the-server-software-developer-specifically-recommends-not-installing-host-based-anti-virus-and-anti-spyware-software-may-address-the-requirement-via-a-network-based-malware-detection-nbmd-solution"></a>Virüsten koruma ve casus yazılımdan koruma, tüm Son Kullanıcı cihazlarında yüklü, çalışan ve güncel olmayan yazılımları tanımlamak ve kaldırmak için sistemlerin düzenli olarak taranmasını sağlar. Sunucu yazılımı geliştiricisinin özellikle konak tabanlı virüsten koruma ve casus yazılımdan koruma yazılımı yüklememesini önerdiği sunucu ortamları, ağ tabanlı kötü amaçlı yazılım algılama (NBMD) çözümü aracılığıyla gereksinimi karşılamayabilir.

**Kimlik**: 0201.09 j1organizational. 124-09. j **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Güvenli uygulamaları tanımlamaya yönelik Uyarlamalı uygulama denetimleri, makinelerinizde etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |Makinelerinizde çalışan bilinen güvenli uygulamaların listesini tanımlamak için uygulama denetimlerini etkinleştirin ve diğer uygulamalar çalıştırıldığında sizi uyarır. Bu, makinelerinizi kötü amaçlı yazılımlara karşı korumanıza yardımcı olur. Güvenlik Merkezi, kurallarınızı yapılandırma ve sürdürme sürecini basitleştirmek için makine öğrenimini kullanarak her makinede çalışan uygulamaları analiz eder ve bilinen güvenli uygulamalar listesini önerir. |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |
|[Windows Server için varsayılan Microsoft ıaasantimalware uzantısını dağıt](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2835b622-407b-4114-9198-6f7064cbe0dc) |Bu ilke, bir VM kötü amaçlı yazılımdan koruma uzantısıyla yapılandırılmadığı zaman bir Microsoft ıaasantimalware uzantısını varsayılan yapılandırmayla dağıtır. |deployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/VMAntimalwareExtension_Deploy.json) |
|[Uç nokta koruma çözümü, sanal makine ölçek kümelerine yüklenmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F26a828e1-e88f-464e-bbb3-c134a282b9de) |Tehdit ve güvenlik açıklarına karşı korumak için sanal makinelerinizdeki bir Endpoint Protection çözümünün varlığını ve sistem durumunu denetleyin. |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssMissingEndpointProtection_Audit.json) |
|[Azure için Microsoft kötü amaçlı yazılımdan koruma imzaları otomatik olarak güncelleştirilecek şekilde yapılandırılmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc43e4a30-77cb-48ab-a4dd-93f175c63b57) |Bu ilke, Microsoft kötü amaçlı yazılımdan koruma imzalarının otomatik güncelleştirmesiyle yapılandırılmayan hiçbir Windows sanal makinesini denetler. |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/VirtualMachines_AntiMalwareAutoUpdate_AuditIfNotExists.json) |
|[Azure Güvenlik Merkezi 'nde eksik Endpoint Protection izleme](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf6cd1bd-1635-48cb-bde7-5b15693900b9) |Yüklü bir Endpoint Protection Aracısı olmayan sunucular, Azure Güvenlik Merkezi tarafından öneriler olarak izlenir |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingEndpointProtection_Audit.json) |
|[Makinelerinize sistem güncelleştirmeleri yüklenmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86b3d65f-7626-441e-b690-81a8b71cff60) |Sunucularınızdaki eksik güvenlik sistemi güncelleştirmeleri, Azure Güvenlik Merkezi tarafından öneriler olarak izlenir |Auditınotexists, devre dışı |[4.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingSystemUpdates_Audit.json) |

## <a name="back-up"></a>Yedekleme

### <a name="backup-copies-of-information-and-software-are-made-and-tests-of-the-media-and-restoration-procedures-are-regularly-performed-at-appropriate-intervals"></a>Bilgilerin ve yazılımların yedek kopyaları yapılır ve medya ve geri yükleme yordamlarını test etmek uygun aralıklarda düzenli olarak gerçekleştirilir.

**Kimlik**: 1616.09 l1organizational. 16-09. l **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Azure SQL veritabanları için uzun vadeli coğrafi olarak yedekli yedeklemenin etkinleştirilmesi gerekir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd38fc420-0735-4ef3-ac11-c806f651a570) |Bu ilke, uzun süreli coğrafi olarak yedekli Yedekleme etkinleştirilmemiş Azure SQL veritabanı 'nı denetler. |Auditınotexists, devre dışı |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_SQLDatabase_AuditIfNotExists.json) |

### <a name="a-formal-definition-of-the-level-of-backup-required-for-each-system-is-defined-and-documented-including-how-each-system-will-be-restored-the-scope-of-data-to-be-imaged-frequency-of-imaging-and-duration-of-retention-based-on-relevant-contractual-legal-regulatory-and-business-requirements"></a>Her bir sistem için gereken yedekleme düzeyinin resmi bir tanımı, her sistemin nasıl geri yükleneceği, yansıma verilecek verilerin kapsamı, görüntü sıklığı ve ilgili sözleşme, yasal, mevzuata ve iş gereksinimlerine bağlı olarak bekletme süresi dahil olmak üzere tanımlanır ve belgelenmiştir.

**Kimlik**: 1617.09 l1organizational. 23-09. l **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[MySQL için Azure veritabanı 'nda coğrafi olarak yedekli yedekleme etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82339799-d096-41ae-8538-b108becf0970) |MySQL için Azure veritabanı, veritabanı sunucunuz için artıklık seçeneğini seçmenize olanak sağlar. Verilerin yalnızca sunucunuzun barındırıldığı bölgede depolanmadığı, ancak aynı zamanda bir bölge hatası durumunda kurtarma seçeneği sağlamak için eşlenmiş bir bölgeye çoğaltılan coğrafi olarak yedekli bir yedekleme depolamasına ayarlanabilir. Yedekleme için coğrafi olarak yedekli depolamayı yapılandırmaya yalnızca sunucu oluşturma sırasında izin verilir. |Denetim, devre dışı |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMySQL_Audit.json) |

### <a name="the-backups-are-stored-in-a-physically-secure-remote-location-at-a-sufficient-distance-to-make-them-reasonably-immune-from-damage-to-data-at-the-primary-site-and-reasonable-physical-and-environmental-controls-are-in-place-to-ensure-their-protection-at-the-remote-location"></a>Yedeklemeler fiziksel olarak güvenli bir uzak konumda depolanır. Bu, birincil sitedeki verilere zarar vermekten bağımsız bir şekilde, ve uzak konumda korunmasını sağlamak için makul fiziksel ve çevre denetimleri sağlar.

**Kimlik**: 1618.09 l1organizational. 45-09. l **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[PostgreSQL için Azure veritabanı için coğrafi olarak yedekli yedekleme etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F48af4db5-9b8b-401c-8e74-076be876a430) |PostgreSQL için Azure veritabanı, veritabanı sunucunuz için artıklık seçeneğini seçmenizi sağlar. Verilerin yalnızca sunucunuzun barındırıldığı bölgede depolanmadığı, ancak aynı zamanda bir bölge hatası durumunda kurtarma seçeneği sağlamak için eşlenmiş bir bölgeye çoğaltılan coğrafi olarak yedekli bir yedekleme depolamasına ayarlanabilir. Yedekleme için coğrafi olarak yedekli depolamayı yapılandırmaya yalnızca sunucu oluşturma sırasında izin verilir. |Denetim, devre dışı |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForPostgreSQL_Audit.json) |

### <a name="inventory-records-for-the-backup-copies-including-content-and-current-location-are-maintained"></a>İçerik ve geçerli konum dahil olmak üzere yedek kopyaların envanter kayıtları korunur.

**Kimlik**: 1619.09 l1organizational. 7-09. l **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[MariaDB için Azure veritabanı 'nda coğrafi olarak yedekli yedekleme etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |MariaDB için Azure veritabanı, veritabanı sunucunuz için artıklık seçeneğini seçmenize olanak sağlar. Verilerin yalnızca sunucunuzun barındırıldığı bölgede depolanmadığı, ancak aynı zamanda bir bölge hatası durumunda kurtarma seçeneği sağlamak için eşlenmiş bir bölgeye çoğaltılan coğrafi olarak yedekli bir yedekleme depolamasına ayarlanabilir. Yedekleme için coğrafi olarak yedekli depolamayı yapılandırmaya yalnızca sunucu oluşturma sırasında izin verilir. |Denetim, devre dışı |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |

### <a name="when-the-backup-service-is-delivered-by-the-third-party-the-service-level-agreement-includes-the-detailed-protections-to-control-confidentiality-integrity-and-availability-of-the-backup-information"></a>Yedekleme hizmeti üçüncü taraf tarafından teslim edildiğinde, hizmet düzeyi sözleşmesi, yedekleme bilgilerinin gizliliğini, bütünlüğünü ve kullanılabilirliğini denetlemek için ayrıntılı korumalar içerir.

**Kimlik**: 1620.09 l1organizational. 8-09. l **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Sanal makineler için Azure Backup etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |Azure Backup etkinleştirerek Azure sanal makinelerinizin korunmasını sağlayın. Azure Backup, Azure için güvenli ve uygun maliyetli bir veri koruma çözümüdür. |Auditınotexists, devre dışı |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |

### <a name="automated-tools-are-used-to-track-all-backups"></a>Otomatikleştirilmiş araçlar tüm yedeklemeleri izlemek için kullanılır.

**Kimlik**: 1621.09 l2organizational. 1-09. l **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Azure SQL veritabanları için uzun vadeli coğrafi olarak yedekli yedeklemenin etkinleştirilmesi gerekir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd38fc420-0735-4ef3-ac11-c806f651a570) |Bu ilke, uzun süreli coğrafi olarak yedekli Yedekleme etkinleştirilmemiş Azure SQL veritabanı 'nı denetler. |Auditınotexists, devre dışı |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_SQLDatabase_AuditIfNotExists.json) |

### <a name="the-integrity-and-security-of-the-backup-copies-are-maintained-to-ensure-future-availability-and-any-potential-accessibility-problems-with-the-backup-copies-are-identified-and-mitigated-in-the-event-of-an-area-wide-disaster"></a>Yedek kopyaların bütünlüğü ve güvenliği, gelecekteki kullanılabilirlik sağlamak için korunur ve alan genelinde bir olağanüstü durum durumunda yedek kopyalarla ilgili olası erişilebilirlik sorunları tanımlanır ve azaltılmıştır.

**Kimlik**: 1622.09 l2organizational. 23-09. l **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[MySQL için Azure veritabanı 'nda coğrafi olarak yedekli yedekleme etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82339799-d096-41ae-8538-b108becf0970) |MySQL için Azure veritabanı, veritabanı sunucunuz için artıklık seçeneğini seçmenize olanak sağlar. Verilerin yalnızca sunucunuzun barındırıldığı bölgede depolanmadığı, ancak aynı zamanda bir bölge hatası durumunda kurtarma seçeneği sağlamak için eşlenmiş bir bölgeye çoğaltılan coğrafi olarak yedekli bir yedekleme depolamasına ayarlanabilir. Yedekleme için coğrafi olarak yedekli depolamayı yapılandırmaya yalnızca sunucu oluşturma sırasında izin verilir. |Denetim, devre dışı |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMySQL_Audit.json) |

### <a name="covered-information-is-backed-up-in-an-encrypted-format-to-ensure-confidentiality"></a>Kapsanan bilgiler, gizliliği sağlamak için şifrelenmiş bir biçimde yedeklenir.

**Kimlik**: 1623.09 l2organizational. 4-09. l **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[PostgreSQL için Azure veritabanı için coğrafi olarak yedekli yedekleme etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F48af4db5-9b8b-401c-8e74-076be876a430) |PostgreSQL için Azure veritabanı, veritabanı sunucunuz için artıklık seçeneğini seçmenizi sağlar. Verilerin yalnızca sunucunuzun barındırıldığı bölgede depolanmadığı, ancak aynı zamanda bir bölge hatası durumunda kurtarma seçeneği sağlamak için eşlenmiş bir bölgeye çoğaltılan coğrafi olarak yedekli bir yedekleme depolamasına ayarlanabilir. Yedekleme için coğrafi olarak yedekli depolamayı yapılandırmaya yalnızca sunucu oluşturma sırasında izin verilir. |Denetim, devre dışı |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForPostgreSQL_Audit.json) |

### <a name="the-organization-performs-incremental-or-differential-backups-daily-and-full-backups-weekly-to-separate-media"></a>Kuruluş, her gün artımlı veya fark yedeklemeler gerçekleştirerek medyayı ayrı ayrı ister.

**Kimlik**: 1624.09 l3organizational. 12-09. l **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[MariaDB için Azure veritabanı 'nda coğrafi olarak yedekli yedekleme etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |MariaDB için Azure veritabanı, veritabanı sunucunuz için artıklık seçeneğini seçmenize olanak sağlar. Verilerin yalnızca sunucunuzun barındırıldığı bölgede depolanmadığı, ancak aynı zamanda bir bölge hatası durumunda kurtarma seçeneği sağlamak için eşlenmiş bir bölgeye çoğaltılan coğrafi olarak yedekli bir yedekleme depolamasına ayarlanabilir. Yedekleme için coğrafi olarak yedekli depolamayı yapılandırmaya yalnızca sunucu oluşturma sırasında izin verilir. |Denetim, devre dışı |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |

### <a name="three-3-generations-of-backups-full-plus-all-related-incremental-or-differential-backups-are-stored-off-site-and-both-on-site-and-off-site-backups-are-logged-with-name-date-time-and-action"></a>Üç (3) yedek nesil (tam ve tüm ilgili artımlı veya fark yedeklemeleri) yerinde depolanır ve hem yerinde hem de site dışı yedeklemeler ad, tarih, saat ve eylem ile günlüğe kaydedilir.

**Kimlik**: 1625.09 l3organizational. 34-09. l **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Sanal makineler için Azure Backup etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |Azure Backup etkinleştirerek Azure sanal makinelerinizin korunmasını sağlayın. Azure Backup, Azure için güvenli ve uygun maliyetli bir veri koruma çözümüdür. |Auditınotexists, devre dışı |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |

### <a name="the-organization-ensures-a-current-retrievable-copy-of-covered-information-is-available-before-movement-of-servers"></a>Kuruluş, sunucuların taşınmadan önce kapsanan bilgilerin geçerli ve alınabilir bir kopyasının kullanılabilir olmasını sağlar.

**Kimlik**: 1626.09 l3organizational. 5-09. l **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[PostgreSQL için Azure veritabanı için coğrafi olarak yedekli yedekleme etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F48af4db5-9b8b-401c-8e74-076be876a430) |PostgreSQL için Azure veritabanı, veritabanı sunucunuz için artıklık seçeneğini seçmenizi sağlar. Verilerin yalnızca sunucunuzun barındırıldığı bölgede depolanmadığı, ancak aynı zamanda bir bölge hatası durumunda kurtarma seçeneği sağlamak için eşlenmiş bir bölgeye çoğaltılan coğrafi olarak yedekli bir yedekleme depolamasına ayarlanabilir. Yedekleme için coğrafi olarak yedekli depolamayı yapılandırmaya yalnızca sunucu oluşturma sırasında izin verilir. |Denetim, devre dışı |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForPostgreSQL_Audit.json) |

### <a name="the-organization-tests-backup-information-following-each-backup-to-verify-media-reliability-and-information-integrity-and-at-least-annually-thereafter"></a>Kuruluş, her yedeklemeden sonra yedekleme bilgilerini test eder ve bu da medya güvenilirliğini ve bilgi tutarlılığını ve en az yılda bir kez daha sonra.

**Kimlik**: 1627.09 l3organizational. 6-09. l **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[MariaDB için Azure veritabanı 'nda coğrafi olarak yedekli yedekleme etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |MariaDB için Azure veritabanı, veritabanı sunucunuz için artıklık seçeneğini seçmenize olanak sağlar. Verilerin yalnızca sunucunuzun barındırıldığı bölgede depolanmadığı, ancak aynı zamanda bir bölge hatası durumunda kurtarma seçeneği sağlamak için eşlenmiş bir bölgeye çoğaltılan coğrafi olarak yedekli bir yedekleme depolamasına ayarlanabilir. Yedekleme için coğrafi olarak yedekli depolamayı yapılandırmaya yalnızca sunucu oluşturma sırasında izin verilir. |Denetim, devre dışı |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |

### <a name="workforce-members-roles-and-responsibilities-in-the-data-backup-process-are-identified-and-communicated-to-the-workforce-in-particular-bring-your-own-device-byod-users-are-required-to-perform-backups-of-organizational-andor-client-data-on-their-devices"></a>Veri yedekleme işlemindeki iş gücü üyeleri rolleri ve sorumlulukları, iş gücüne göre tanımlanır ve iletilir; Özellikle, kendi cihazını getir (KCG) kullanıcılarının, cihazlarındaki kuruluş ve/veya istemci verilerinin yedeklerini gerçekleştirmesi gerekir.

**Kimlik**: 1699.09 l1organizational. 10-09. l **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Sanal makineler için Azure Backup etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |Azure Backup etkinleştirerek Azure sanal makinelerinizin korunmasını sağlayın. Azure Backup, Azure için güvenli ve uygun maliyetli bir veri koruma çözümüdür. |Auditınotexists, devre dışı |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |

## <a name="network-controls"></a>Ağ denetimleri

### <a name="the-organization-monitors-for-all-authorized-and-unauthorized-wireless-access-to-the-information-system-and-prohibits-installation-of-wireless-access-points-waps-unless-explicitly-authorized-in-writing-by-the-cio-or-hisher-designated-representative"></a>Kuruluş, bilgi sistemine tüm yetkili ve yetkisiz kablosuz erişimi izler ve CIO 'nun veya belirtilen temsilcisi tarafından yazma sırasında açıkça yetkilendirilmediği takdirde kablosuz erişim noktalarının (WAP 'ler) yüklenmesini yasaklar.

**Kimlik**: 0858.09 m1organizational. 4-09. d **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Tüm ağ bağlantı noktaları, sanal makineniz ile ilişkili ağ güvenlik gruplarında sınırlandırılmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9daedab3-fb2d-461e-b861-71790eead4f6) |Azure Güvenlik Merkezi, bazı ağ güvenlik gruplarınızı gelen kurallarınızı çok fazla izin verecek şekilde tanımladı. Gelen kurallar ' any ' veya ' Internet ' aralıklarından erişime izin vermez. Bu, saldırganların kaynaklarınızı hedeflemesini sağlayabilir. |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnprotectedEndpoints_Audit.json) |
|[Sanal makinelerin yönetim bağlantı noktaları, tam zamanında ağ erişim denetimiyle korunmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |Olası ağ tam zamanında (JıT) erişim, Azure Güvenlik Merkezi tarafından öneriler olarak izlenir |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |
|[Windows makineleri ' Windows Güvenlik Duvarı Özellikleri ' için gereksinimleri karşılamalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F35d9882c-993d-44e6-87d2-db66ce21b636) |Windows makineleri, güvenlik duvarı durumu, bağlantılar, kural yönetimi ve bildirimler için ' Windows Güvenlik Duvarı Özellikleri ' kategorisinde belirtilen grup ilkesi ayarlara sahip olmalıdır. Bu ilke, Konuk yapılandırma ön koşullarının ilke atama kapsamına dağıtılmasını gerektirir. Ayrıntılar için, adresini ziyaret edin [https://aka.ms/gcpol](https://aka.ms/gcpol) . |Auditınotexists, devre dışı |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsFirewallProperties_AINE.json) |

### <a name="the-organization-ensures-the-security-of-information-in-networks-availability-of-network-services-and-information-services-using-the-network-and-the-protection-of-connected-services-from-unauthorized-access"></a>Kuruluş, ağlardaki bilgilerin güvenliğini, ağı kullanarak ağ hizmetlerinin ve bilgi hizmetlerinin kullanılabilirliğine ve bağlı hizmetlerin yetkisiz erişime karşı korunmasını sağlar.

**Kimlik**: 0859.09 m1organizational. 78-09. d **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Uyarlamalı ağ sağlamlaştırma önerileri internet 'e yönelik sanal makinelere uygulanmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure Güvenlik Merkezi, Internet 'e yönelik sanal makinelerin trafik düzenlerini analiz eder ve olası saldırı yüzeyini azaltan ağ güvenlik grubu kuralı önerileri sağlar |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |

### <a name="the-organization-formally-manages-equipment-on-the-network-including-equipment-in-user-areas"></a>Kuruluş, Kullanıcı alanlarındaki ekipman dahil olmak üzere ağdaki donatımı resmi olarak yönetir.

**Kimlik**: 0860.09 m1organizational. 9-09. d **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Ağ güvenlik grupları için tanılama ayarlarını dağıtma](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc9c29499-c1d1-4195-99bd-2ec9e3a9dc89) |Bu ilke, tanılama ayarlarını otomatik olarak ağ güvenlik gruplarına dağıtır. ' {StoragePrefixParameter} {NSGLocation} ' adlı bir depolama hesabı otomatik olarak oluşturulacak. |deployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/DiagnosticSettingsForNSG_Deploy.json) |
|[Service Bus bir sanal ağ hizmeti uç noktası kullanmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F235359c5-7c52-4b82-9055-01c75cf9f60e) |Bu ilke, bir sanal ağ hizmeti uç noktası kullanmak üzere yapılandırılmamış Service Bus denetler. |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ServiceBus_AuditIfNotExists.json) |

### <a name="to-identify-and-authenticate-devices-on-local-andor-wide-area-networks-including-wireless-networks-nbspthe-information-system-uses-either-a-i-shared-known-information-solution-or-ii-an-organizational-authentication-solution-the-exact-selection-and-strength-of-which-is-dependent-on-the-security-categorization-of-the-information-system"></a>Kablosuz ağlar dahil olmak üzere yerel ve/veya geniş ağlardaki cihazları tanımlamak ve doğrulamak için, &nbsp; bilgi sistemi bir (i) paylaşılan bilinen bilgi çözümünü ya da (ii) bir kurumsal kimlik doğrulama çözümü kullanır, tam seçim ve kuvvetini bilgi sisteminin güvenlik kategorilerine bağımlıdır.

**Kimlik**: 0861.09 m2organizational. 67-09. d **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[App Service bir sanal ağ hizmeti uç noktası kullanmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2d21331d-a4c2-4def-a9ad-ee4e1e023beb) |Bu ilke, bir sanal ağ hizmeti uç noktası kullanmak üzere yapılandırılmamış App Service denetler. |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_AppService_AuditIfNotExists.json) |
|[Windows makineleri, ' güvenlik seçenekleri-ağ erişimi ' için gereksinimleri karşılamalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3ff60f98-7fa4-410a-9f7f-0b00f5afdbdd) |Windows makineler, anonim kullanıcılar, yerel hesaplar ve kayıt defterine uzaktan erişim dahil olmak üzere ' güvenlik seçenekleri-ağ erişimi ' kategorisinde belirtilen grup ilkesi ayarlara sahip olmalıdır. Bu ilke, Konuk yapılandırma ön koşullarının ilke atama kapsamına dağıtılmasını gerektirir. Ayrıntılar için, adresini ziyaret edin [https://aka.ms/gcpol](https://aka.ms/gcpol) . |Auditınotexists, devre dışı |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecurityOptionsNetworkAccess_AINE.json) |

### <a name="the-organization-ensures-information-systems-protect-the-confidentiality-and-integrity-of-transmitted-information-including-during-preparation-for-transmission-and-during-reception"></a>Kuruluş bilgi sistemlerinin, iletim hazırlığı ve alım sırasında da dahil olmak üzere aktarılan bilgilerin gizliliğini ve bütünlüğünü korumasını sağlar.

**Kimlik**: 0862.09 m2organizational. 8-09. d **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[SQL Server bir sanal ağ hizmeti uç noktası kullanmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fae5d2f14-d830-42b6-9899-df6cfe9c71a3) |Bu ilke, bir sanal ağ hizmeti uç noktası kullanmak üzere yapılandırılmamış SQL Server denetler. |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_SQLServer_AuditIfNotExists.json) |

### <a name="the-organization-builds-a-firewall-configuration-that-restricts-connections-between-un-trusted-networks-and-any-system-components-in-the-covered-information-environment-and-any-changes-to-the-firewall-configuration-are-updated-in-the-network-diagram"></a>Kuruluş, güvenilir olmayan ağlar ile kapsanan bilgi ortamındaki tüm sistem bileşenleri arasındaki bağlantıları kısıtlayan bir güvenlik duvarı yapılandırması oluşturur; ve güvenlik duvarı yapılandırmasındaki değişiklikler ağ diyagramında güncelleştirilir.

**Kimlik**: 0863.09 m2organizational. 910-09. d **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Olay Hub 'ı bir sanal ağ hizmeti uç noktası kullanmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd63edb4a-c612-454d-b47d-191a724fcbf0) |Bu ilke, bir sanal ağ hizmeti uç noktası kullanacak şekilde yapılandırılmamış tüm olay hub 'ını denetler. |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_EventHub_AuditIfNotExists.json) |

### <a name="usage-restrictions-and-implementation-guidance-are-formally-defined-for-voip-including-the-authorization-and-monitoring-of-the-service"></a>Kullanım kısıtlamaları ve uygulama kılavuzu, hizmetin yetkilendirmesi ve izlenmesi dahil olmak üzere VoIP için tanımlanır.

**Kimlik**: 0864.09 m2organizational. 12-09. d **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Cosmos DB bir sanal ağ hizmeti uç noktası kullanmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe0a2b1a3-f7f9-4569-807f-2a9edebdf4d9) |Bu ilke, bir sanal ağ hizmeti uç noktası kullanmak üzere yapılandırılmamış Cosmos DB denetler. |Denetim, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_CosmosDB_Audit.json) |

### <a name="the-organization-i-authorizes-connections-from-the-information-system-to-other-information-systems-outside-of-the-organization-through-the-use-of-interconnection-security-agreements-or-other-formal-agreement-ii-documents-each-connection-the-interface-characteristics-security-requirements-and-the-nature-of-the-information-communicated-iii-employs-a-deny-all-permit-by-exception-policy-for-allowing-connections-from-the-information-system-to-other-information-systems-outside-of-the-organization-and-iv-applies-a-default-deny-rule-that-drops-all-traffic-via-host-based-firewalls-or-port-filtering-tools-on-its-endpoints-workstations-servers-etc-except-those-services-and-ports-that-are-explicitly-allowed"></a>Kuruluş (i), bağlantı güvenliği sözleşmelerinin veya diğer biçimsel sözleşmenin kullanımı aracılığıyla bilgi sisteminden kuruluş dışındaki diğer bilgi sistemlerine yönelik bağlantıları yetkilendirir. (ii) her bir bağlantıyı, arabirim özelliklerini, güvenlik gereksinimlerini ve iletilen bilgilerin doğasını belgeler; (III) bilgi sisteminden kuruluş dışındaki diğer bilgi sistemlerine yönelik bağlantılara izin vermek için bir Tümünü Reddet, özel durum ilkesine izin ver ilkesi kullanır; ve (IV), açıkça izin verilen hizmetler ve bağlantı noktaları hariç, tüm trafiği uç noktalarında (iş istasyonları, sunucular, vb.) ana bilgisayar tabanlı güvenlik duvarları veya bağlantı noktası filtreleme araçları aracılığıyla alan bir varsayılan reddetme kuralı uygular.

**Kimlik**: 0865.09 m2organizational. 13-09. d **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Key Vault bir sanal ağ hizmeti uç noktası kullanmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea4d6841-2173-4317-9747-ff522a45120f) |Bu ilke, bir sanal ağ hizmeti uç noktası kullanmak üzere yapılandırılmamış Key Vault denetler. |Denetim, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_KeyVault_Audit.json) |

### <a name="the-organization-describes-the-groups-roles-and-responsibilities-for-the-logical-management-of-network-components-and-ensures-coordination-of-and-consistency-in-the-elements-of-the-network-infrastructure"></a>Kuruluş, ağ bileşenlerinin mantıksal yönetimine ilişkin grupları, rolleri ve sorumlulukları açıklar ve ağ altyapısının öğelerinde koordine ve tutarlılık sağlar.

**Kimlik**: 0866.09 m3organizational. 1516-09. d **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Depolama hesapları, ağ erişimini kısıtlıyor olmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34c877ad-507e-4c82-993e-3452a6e0ad3c) |Depolama hesaplarına ağ erişimi kısıtlanmalıdır. Ağ kurallarını, yalnızca izin verilen ağların uygulamalarının depolama hesabına erişebilmesi için yapılandırın. Belirli internet veya şirket içi istemcilerden gelen bağlantılara izin vermek için, belirli Azure sanal ağlarından veya genel İnternet IP adresi aralıklarına yönelik trafiğe erişim verilebilir |Denetim, reddetme, devre dışı |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |

### <a name="wireless-access-points-are-placed-in-secure-areas-and-shut-down-when-not-in-use-eg-nights-weekends"></a>Kablosuz erişim noktaları güvenli alanlara yerleştirilir ve kullanımda olmadığında (örn. gece, hafta sonları) kapanır.

**Kimlik**: 0867.09 m3organizational. 17-09. d **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Depolama hesapları bir sanal ağ hizmeti uç noktası kullanmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F60d21c4f-21a3-4d94-85f4-b924e6aeeda4) |Bu ilke, bir sanal ağ hizmeti uç noktası kullanmak üzere yapılandırılmamış tüm depolama hesaplarını denetler. |Denetim, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_StorageAccount_Audit.json) |

### <a name="the-organization-builds-a-firewall-configuration-to-restrict-inbound-and-outbound-traffic-to-that-which-is-necessary-for-the-covered-data-environment"></a>Kuruluş, gelen ve giden trafiği kapsanan veri ortamı için gerekli olanlarla kısıtlamak için bir güvenlik duvarı yapılandırması oluşturur.

**Kimlik**: 0868.09 m3organizational. 18-09. d **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Container Registry bir sanal ağ hizmeti uç noktası kullanmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |Bu ilke, bir sanal ağ hizmeti uç noktası kullanmak üzere yapılandırılmamış Container Registry denetler. |Denetim, devre dışı |[1.0.0-Önizleme](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |

### <a name="the-router-configuration-files-are-secured-and-synchronized"></a>Yönlendirici yapılandırma dosyaları güvenlidir ve eşitlenir.

**Kimlik**: 0869.09 m3organizational. 19-09. d **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Container Registry bir sanal ağ hizmeti uç noktası kullanmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |Bu ilke, bir sanal ağ hizmeti uç noktası kullanmak üzere yapılandırılmamış Container Registry denetler. |Denetim, devre dışı |[1.0.0-Önizleme](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |

### <a name="access-to-all-proxies-is-denied-except-for-those-hosts-ports-and-services-that-are-explicitly-required"></a>Açık olarak gerekli olan konaklar, bağlantı noktaları ve hizmetler dışında tüm proxy 'lerine erişim reddedilir.

**Kimlik**: 0870.09 m3organizational. 20-09. d **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Container Registry bir sanal ağ hizmeti uç noktası kullanmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |Bu ilke, bir sanal ağ hizmeti uç noktası kullanmak üzere yapılandırılmamış Container Registry denetler. |Denetim, devre dışı |[1.0.0-Önizleme](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |

### <a name="authoritative-dns-servers-are-segregated-into-internal-and-external-roles"></a>Yetkili DNS sunucuları iç ve dış rollere bölünmüştür.

**Kimlik**: 0871.09 m3organizational. 22-09. d **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Container Registry bir sanal ağ hizmeti uç noktası kullanmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |Bu ilke, bir sanal ağ hizmeti uç noktası kullanmak üzere yapılandırılmamış Container Registry denetler. |Denetim, devre dışı |[1.0.0-Önizleme](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |

## <a name="security-of-network-services"></a>Ağ hizmetlerinin güvenliği

### <a name="agreed-services-provided-by-a-network-service-providermanager-are-formally-managed-and-monitored-to-ensure-they-are-provided-securely"></a>Bir ağ hizmeti sağlayıcısı/Yöneticisi tarafından sunulan kabul edilen hizmetler, güvenli bir şekilde sağlandıklarından emin olmak için resmi olarak yönetilir ve izlenir.

**Kimlik**: 0835.09 n1organizational. 1-09. n **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Windows sanal makinelerine ağ trafiği veri toplama Aracısı yüklenmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2f2ee1de-44aa-4762-b6bd-0893fc3f306d) |Güvenlik Merkezi, ağ eşlemesinde trafik görselleştirme, ağ sağlamlaştırma önerileri ve belirli ağ tehditleri gibi gelişmiş ağ koruma özelliklerini etkinleştirmek üzere Azure sanal makinelerinizdeki ağ trafiği verilerini toplamak için Microsoft bağımlılık Aracısı 'nı kullanır. |Auditınotexists, devre dışı |[1.0.1-Önizleme](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ASC_Dependency_Agent_Audit_Windows.json) |
|[Sanal makinelerin yeni Azure Resource Manager kaynaklara geçirilmesi gerekir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1d84d5fb-01f6-4d12-ba4f-4a26081d403d) |Sanal makinelerinize yönelik yeni Azure Resource Manager kullanarak: daha güçlü erişim denetimi (RBAC), daha iyi denetim, Azure Resource Manager tabanlı dağıtım ve idare, yönetilen kimliklere erişim, gizli dizi için anahtar kasasına erişme, parolaların ve kaynak grupları için Azure AD tabanlı kimlik doğrulama ve destek daha kolay güvenlik yönetimi sağlar |Denetim, reddetme, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/ClassicCompute_Audit.json) |

### <a name="the-organization-formally-authorizes-and-documents-the-characteristics-of-each-connection-from-an-information-system-to-other-information-systems-outside-the-organization"></a>Kuruluş, bir bilgi sisteminden, kuruluşun dışındaki diğer bilgi sistemlerine her bağlantının özelliklerini bir şekilde yetkilendirir ve belgeler.

**Kimlik**: 0836.09. n2Organizational. 1-09. n **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Ağ trafiği veri toplama Aracısı Linux sanal makinelerine yüklenmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F04c4380f-3fae-46e8-96c9-30193528f602) |Güvenlik Merkezi, ağ eşlemesinde trafik görselleştirme, ağ sağlamlaştırma önerileri ve belirli ağ tehditleri gibi gelişmiş ağ koruma özelliklerini etkinleştirmek üzere Azure sanal makinelerinizdeki ağ trafiği verilerini toplamak için Microsoft bağımlılık Aracısı 'nı kullanır. |Auditınotexists, devre dışı |[1.0.1-Önizleme](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ASC_Dependency_Agent_Audit_Linux.json) |

### <a name="formal-agreements-with-external-information-system-providers-include-specific-obligations-for-security-and-privacy"></a>Dış bilgi sistem sağlayıcılarıyla resmi anlaşmalar, güvenlik ve gizlilik için özel yükümlülükleri içerir.

**Kimlik**: 0837.09. n2Organizational. 2-09. n **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Ağ Izleyicisi etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6e2945c-0b7b-40f5-9233-7a5323b5cdc6) |Ağ Izleyicisi, Azure 'daki ve Azure 'dan bir ağ senaryosu düzeyinde koşulları izlemenizi ve tanılamanıza olanak tanıyan bölgesel bir hizmettir. Senaryo düzeyi izleme, bir uçtan uca ağ düzeyi görünümündeki sorunları tanılamanıza olanak sağlar. Ağ Izleyicisi ile kullanılabilen ağ tanılama ve görselleştirme araçları, Azure 'da ağınızı anlamanıza, tanılamanıza ve elde etmenize yardımcı olur. |Auditınotexists, devre dışı |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |

### <a name="the-organization-reviews-and-updates-the-interconnection-security-agreements-on-an-ongoing-basis-verifying-enforcement-of-security-requirements"></a>Kuruluş, güvenlik gereksinimlerinin uygulanmasını doğrulayan, bağlı güvenlik sözleşmelerini sürekli olarak inceler ve güncelleştirir.

**Kimlik**: 0885.09 n2organizational. 3-09. n **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Ağ trafiği veri toplama Aracısı Linux sanal makinelerine yüklenmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F04c4380f-3fae-46e8-96c9-30193528f602) |Güvenlik Merkezi, ağ eşlemesinde trafik görselleştirme, ağ sağlamlaştırma önerileri ve belirli ağ tehditleri gibi gelişmiş ağ koruma özelliklerini etkinleştirmek üzere Azure sanal makinelerinizdeki ağ trafiği verilerini toplamak için Microsoft bağımlılık Aracısı 'nı kullanır. |Auditınotexists, devre dışı |[1.0.1-Önizleme](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ASC_Dependency_Agent_Audit_Linux.json) |

### <a name="the-organization-employs-and-documents-in-a-formal-agreement-or-other-document-either-i-allow-all-deny-by-exception-or-ii-deny-all-permit-by-exception-preferred-policy-for-allowing-specific-information-systems-to-connect-to-external-information-systems"></a>Kuruluş, belirli bilgi sistemlerinin dış bilgi sistemlerine bağlanmasına izin vermek için bir biçimsel anlaşma veya başka bir belgedeki belge (i), tüm özel durum reddi veya, II) Reddet-tümü, özel durum (tercih edilen) ilkesi için ilke kullanır.

**Kimlik**: 0886.09 n2organizational. 4-09. n **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Ağ Izleyicisi etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6e2945c-0b7b-40f5-9233-7a5323b5cdc6) |Ağ Izleyicisi, Azure 'daki ve Azure 'dan bir ağ senaryosu düzeyinde koşulları izlemenizi ve tanılamanıza olanak tanıyan bölgesel bir hizmettir. Senaryo düzeyi izleme, bir uçtan uca ağ düzeyi görünümündeki sorunları tanılamanıza olanak sağlar. Ağ Izleyicisi ile kullanılabilen ağ tanılama ve görselleştirme araçları, Azure 'da ağınızı anlamanıza, tanılamanıza ve elde etmenize yardımcı olur. |Auditınotexists, devre dışı |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |

### <a name="the-organization-requires-externaloutsourced-service-providers-to-identify-the-specific-functions-ports-and-protocols-used-in-the-provision-of-the-externaloutsourced-services"></a>Kuruluş, dış/dış kaynak hizmetleri sağlamak için kullanılan belirli işlevleri, bağlantı noktalarını ve protokolleri belirlemek için dış/dış kaynaklı hizmet sağlayıcıları gerektirir.

**Kimlik**: 0887.09 n2organizational. 5-09. n **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Windows sanal makinelerine ağ trafiği veri toplama Aracısı yüklenmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2f2ee1de-44aa-4762-b6bd-0893fc3f306d) |Güvenlik Merkezi, ağ eşlemesinde trafik görselleştirme, ağ sağlamlaştırma önerileri ve belirli ağ tehditleri gibi gelişmiş ağ koruma özelliklerini etkinleştirmek üzere Azure sanal makinelerinizdeki ağ trafiği verilerini toplamak için Microsoft bağımlılık Aracısı 'nı kullanır. |Auditınotexists, devre dışı |[1.0.1-Önizleme](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ASC_Dependency_Agent_Audit_Windows.json) |

### <a name="the-contract-with-the-externaloutsourced-service-provider-includes-the-specification-that-the-service-provider-is-responsible-for-the-protection-of-covered-information-shared"></a>Dış/dış kaynaklı hizmet sağlayıcısına sahip sözleşme, hizmet sağlayıcının, kapsanan bilgilerin paylaşılmasından sorumlu olduğunu içerir.

**Kimlik**: 0888.09 n2organizational. 6-09. n **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Ağ Izleyicisi etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6e2945c-0b7b-40f5-9233-7a5323b5cdc6) |Ağ Izleyicisi, Azure 'daki ve Azure 'dan bir ağ senaryosu düzeyinde koşulları izlemenizi ve tanılamanıza olanak tanıyan bölgesel bir hizmettir. Senaryo düzeyi izleme, bir uçtan uca ağ düzeyi görünümündeki sorunları tanılamanıza olanak sağlar. Ağ Izleyicisi ile kullanılabilen ağ tanılama ve görselleştirme araçları, Azure 'da ağınızı anlamanıza, tanılamanıza ve elde etmenize yardımcı olur. |Auditınotexists, devre dışı |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |

## <a name="management-of-removable-media"></a>Çıkarılabilir medya yönetimi

### <a name="the-organization-based-on-the-data-classification-level-registers-media-including-laptops-prior-to-use-places-reasonable-restrictions-on-how-such-media-be-used-and-provides-an-appropriate-level-of-physical-and-logical-protection-including-encryption-for-media-containing-covered-information-until-properly-destroyed-or-sanitized"></a>Veri sınıflandırma düzeyine bağlı olarak, medyayı kullanımdan önce (dizüstü bilgisayarlar dahil) kaydeder, bu tür ortamların nasıl kullanılacağına dair makul kısıtlamalar getirir ve uygun bir fiziksel ve mantıksal koruma düzeyi (şifreleme dahil), doğru bir şekilde yok edilir veya yok edilebilir.

**Kimlik**: 0301.09 o1organizational. 123-09. o **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[SQL veritabanlarındaki Saydam Veri Şifrelemesi etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17k78e20-9358-41c9-923c-fb736d382a12) |Rest verilerini korumak ve uyumluluk gereksinimlerini karşılamak için saydam veri şifrelemesi etkinleştirilmelidir |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlDBEncryption_Audit.json) |

### <a name="the-organization-protects-and-controls-media-containing-sensitive-information-during-transport-outside-of-controlled-areas"></a>Kuruluş, denetlenen alanların dışında taşıma sırasında hassas bilgiler içeren medyayı korur ve denetler.

**Kimlik**: 0302.09 o2organizational. 1-09. o **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Disk şifrelemesi sanal makinelere uygulanmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0961003e-5a0a-4549-abde-af6a37f2724d) |Etkin bir disk şifrelemesi olmayan sanal makineler, Azure Güvenlik Merkezi tarafından öneriler olarak izlenir. |Auditınotexists, devre dışı |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnencryptedVMDisks_Audit.json) |

### <a name="digital-and-non-digital-media-requiring-restricted-use-and-the-specific-safeguards-used-to-restrict-their-use-are-identified"></a>Sınırlı kullanım gerektiren dijital ve dijital olmayan medya, kullanımlarını kısıtlamak için kullanılan belirli korumalar tanımlanmıştır.

**Kimlik**: 0303.09 o2organizational. 2-09. o **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Eklenmemiş diskler şifrelenmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c89a2e5-7285-40fe-afe0-ae8654b92fb2) |Bu ilke, şifrelemeden önce eklenmemiş tüm diskleri denetler. |Denetim, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/UnattachedDisk_Encryption_Audit.json) |

### <a name="the-organization-restricts-the-use-of-writable-removable-media-and-personally-owned-removable-media-in-organizational-systems"></a>Kuruluş, Şirket sistemlerinde yazılabilir çıkarılabilir medya ve kişisel olarak sahip çıkarılabilir medya kullanımını kısıtlar.

**Kimlik**: 0304.09 o3organizational. 1-09. o **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Data Lake Store hesaplarında şifreleme gerektir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa7ff3161-0087-490a-9ad9-ad6217f4f43a) |Bu ilke, tüm Data Lake Store hesaplarında şifrelemenin etkinleştirilmesini sağlar |reddedebilir |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeStoreEncryption_Deny.json) |
|[SQL yönetilen örnekler, bekleyen verileri şifrelemek için müşteri tarafından yönetilen anahtarları kullanmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F048248b0-55cd-46da-b1ff-39efd52db260) |Kendi anahtarınızla Saydam Veri Şifrelemesi (TDE) uygulamak, TDE koruyucu üzerinde daha fazla saydamlık ve denetim, HSM ile desteklenen bir dış hizmetle daha fazla güvenlik ve görev ayrımı ile ilgili yükseltme sağlar. Bu öneri, ilişkili bir uyumluluk gereksinimi olan kuruluşlar için geçerlidir. |Auditınotexists, devre dışı |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |
|[SQL Server 'lar, bekleyen verileri şifrelemek için müşteri tarafından yönetilen anahtarları kullanmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0d134df8-db83-46fb-ad72-fe0c9428c8dd) |Kendi anahtarınızla Saydam Veri Şifrelemesi (TDE) uygulamak, TDE koruyucusu üzerinde daha fazla saydamlık ve denetim, HSM ile desteklenen bir dış hizmetle daha fazla güvenlik ve görev ayrımı ile ilgili yükseltme sağlar. Bu öneri, ilişkili bir uyumluluk gereksinimi olan kuruluşlar için geçerlidir. |Auditınotexists, devre dışı |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |

## <a name="information-exchange-policies-and-procedures"></a>Bilgi değişimi Ilkeleri ve yordamları

### <a name="cloud-service-providers-use-an-industry-recognized-virtualization-platform-and-standard-virtualization-formats-eg-open-virtualization-format-ovf-to-help-ensure-interoperability-and-has-documented-custom-changes-made-to-any-hypervisor-in-use-and-all-solution-specific-virtualization-hooks-available-for-customer-review"></a>Bulut hizmeti sağlayıcıları, birlikte çalışabilirlik sağlamaya yardımcı olmak için sektör tarafından tanınan bir sanallaştırma platformu ve standart sanallaştırma biçimleri (örn., Open Virtualization Format, OVF) kullanır ve kullanımda olan herhangi bir Hiper yöneticide yapılan özel değişiklikler ve müşteri incelemesi için tüm çözüme özgü sanallaştırma kancalarını belgelemiştir.

**Kimlik**: 0662.09 Scspkuruluş. 2-09. s **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[WEB uygulamasının ' Istemci sertifikaları (gelen istemci sertifikaları) ' olarak ' on ' olarak ayarlandığından emin olun](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5bb220d9-2698-4ee4-8404-b9c30c9df609) |İstemci sertifikaları, uygulamanın gelen istekler için bir sertifika istemesine izin verir. Yalnızca geçerli sertifikaya sahip istemciler uygulamaya erişebiliyor. |Denetim, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Webapp_Audit_ClientCert.json) |

### <a name="the-organization-formally-addresses-multiple-safeguards-before-allowing-the-use-of-information-systems-for-information-exchange"></a>Kuruluş, bilgi değişimi için bilgi sistemlerinin kullanılmasına izin vermeden önce birden çok koruyucu tarafından ele alınmaktadır.

**Kimlik**: 0901.09 s1organizational. 1-09. s **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[CORS, her kaynağın Web Uygulamalarınıza erişmesine izin vermemelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5744710e-cc2f-4ee8-8809-3b11e89f4bc9) |Çıkış noktaları arası kaynak paylaşımı (CORS), tüm etki alanlarının Web uygulamanıza erişmesine izin vermemelidir. Yalnızca gerekli etki alanlarının Web uygulamanızla etkileşime girmesine izin verin. |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RestrictCORSAccess_WebApp_Audit.json) |

### <a name="remote-external-access-to-the-organizations-information-assets-and-access-to-external-information-assets-for-which-the-organization-has-no-control-is-based-on-clearly-defined-terms-and-conditions"></a>Kuruluşun bilgi varlıklarına uzak (harici) erişim ve dış bilgi varlıklarına erişim (kuruluşun hiçbir denetimi olmadığı), açıkça tanımlanmış hüküm ve koşullara dayanır.

**Kimlik**: 0902.09 s2organizational. 13-09. s **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[CORS, her kaynağın Işlev uygulamalarınıza erişmesine izin vermemelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0820b7b9-23aa-4725-a1ce-ae4558f718e5) |Çıkış noktaları arası kaynak paylaşımı (CORS), tüm etki alanlarının Işlev uygulamanıza erişmesine izin vermemelidir. Yalnızca gerekli etki alanlarının Işlev uygulamanızla etkileşime girmesine izin verin. |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RestrictCORSAccess_FuntionApp_Audit.json) |

### <a name="the-organization-establishes-terms-and-conditions-consistent-with-any-trust-relationship-established-with-other-organizations-owning-operating-andor-maintaining-external-information-systems-allowing-authorized-individuals-to-i-access-the-information-system-from-external-information-systems-and-ii-process-store-or-transmit-organization-controlled-information-using-external-information-systems"></a>Kuruluş, dış bilgi sistemlerini çalıştıran, çalışan ve/veya dışarıdaki kişilerin bilgi sistemine dış bilgi sistemlerinden erişmesine izin veren, diğer kuruluşlara yönelik hüküm ve koşullar oluşturur. ve (ii) dış bilgi sistemlerini kullanarak kuruluş denetimli bilgileri işleyin, depolayın veya iletir.

**Kimlik**: 0911.09 s1organizational. 2-09. s **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[CORS, her kaynağın API uygulamanıza erişmesine izin vermemelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F358c20a6-3f9e-4f0e-97ff-c6ce485e2aac) |Çıkış noktaları arası kaynak paylaşımı (CORS), tüm etki alanlarının API uygulamanıza erişmesine izin vermemelidir. Yalnızca gerekli etki alanlarının API uygulamanız ile etkileşime girmesine izin verin. |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RestrictCORSAccess_ApiApp_Audit.json) |

### <a name="cryptography-is-used-to-protect-the-confidentiality-and-integrity-of-remote-access-sessions-to-the-internal-network-and-to-external-systems"></a>Şifreleme, uzaktan erişim oturumlarının dahili ağa ve dış sistemlere gizliliğini ve bütünlüğünü korumak için kullanılır.

**Kimlik**: 0912.09 s1organizational. 4-09. s **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Web uygulamaları için uzaktan hata ayıklama kapatılmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcb510bfd-1cba-4d9f-a230-cb0976f4bb71) |Uzaktan hata ayıklama, gelen bağlantı noktalarının bir Web uygulamasında açılmasını gerektirir. Uzaktan hata ayıklama kapalı olmalıdır. |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_WebApp_Audit.json) |

### <a name="strong-cryptography-protocols-are-used-to-safeguard-covered-information-during-transmission-over-less-trusted--open-public-networks"></a>Güçlü şifreleme protokolleri, daha az güvenilir/açık ortak ağlarla iletim sırasında kapsanan bilgileri korumak için kullanılır.

**Kimlik**: 0913.09 s1organizational. 5-09. s **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Işlev uygulamaları için uzaktan hata ayıklama kapatılmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e60b895-3786-45da-8377-9c6b4b6ac5f9) |Uzaktan hata ayıklama, işlev uygulamalarında gelen bağlantı noktalarının açılmasını gerektirir. Uzaktan hata ayıklama kapalı olmalıdır. |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_FunctionApp_Audit.json) |

### <a name="the-organization-ensures-that-communication-protection-requirements-including-the-security-of-exchanges-of-information-is-the-subject-of-policy-development-and-compliance-audits"></a>Kuruluş, bilgi değişimlerinin güvenliği de dahil olmak üzere iletişim koruma gereksinimlerinin ilke geliştirme ve uyumluluk denetimlerinin konusu olmasını sağlar.

**Kimlik**: 0914.09 s1organizational. 6-09. s **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[API Apps için uzaktan hata ayıklama kapatılmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe9c8d085-d9cc-4b17-9cdc-059f1f01f19e) |Uzaktan hata ayıklama, API uygulamalarında gelen bağlantı noktalarının açılmasını gerektirir. Uzaktan hata ayıklama kapalı olmalıdır. |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_ApiApp_Audit.json) |

### <a name="the-organization-limits-the-use-of-organization-controlled-portable-storage-media-by-authorized-individuals-on-external-information-systems"></a>Kuruluş, şirket tarafından denetlenen taşınabilir depolama ortamının kullanımını, dış bilgi sistemlerindeki yetkili kişiler tarafından sınırlandırır.

**Kimlik**: 0915.09 s2organizational. 2-09. s **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[WEB uygulamasının ' Istemci sertifikaları (gelen istemci sertifikaları) ' olarak ' on ' olarak ayarlandığından emin olun](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5bb220d9-2698-4ee4-8404-b9c30c9df609) |İstemci sertifikaları, uygulamanın gelen istekler için bir sertifika istemesine izin verir. Yalnızca geçerli sertifikaya sahip istemciler uygulamaya erişebiliyor. |Denetim, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Webapp_Audit_ClientCert.json) |

### <a name="the-information-system-prohibits-remote-activation-of-collaborative-computing-devices-and-provides-an-explicit-indication-of-use-to-users-physically-present-at-the-devices"></a>Bilgi sistemi, işbirliğine dayalı bilgi işlem cihazlarının uzaktan etkinleştirilmesini yasaklar ve cihazlarda fiziksel olarak bulunan kullanıcılara açık bir gösterge sağlar.

**Kimlik**: 0916.09 s2organizational. 4-09. s **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[CORS, her kaynağın Web Uygulamalarınıza erişmesine izin vermemelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5744710e-cc2f-4ee8-8809-3b11e89f4bc9) |Çıkış noktaları arası kaynak paylaşımı (CORS), tüm etki alanlarının Web uygulamanıza erişmesine izin vermemelidir. Yalnızca gerekli etki alanlarının Web uygulamanızla etkileşime girmesine izin verin. |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RestrictCORSAccess_WebApp_Audit.json) |

### <a name="cloud-service-providers-use-secure-eg-non-clear-text-and-authenticated-standardized-network-protocols-for-the-import-and-export-of-data-and-to-manage-the-service-and-make-available-a-document-to-consumers-tenants-detailing-the-relevant-interoperability-and-portability-standards-that-are-involved"></a>Bulut hizmeti sağlayıcıları, verilerin içeri ve dışarı aktarılması ve hizmetin yönetilmesi için güvenli (ör. şifresiz metin ve kimlik doğrulamalı) standartlaştırılmış ağ protokolleri kullanır ve ilgili birlikte çalışabilirlik ve taşınabilirlik standartlarının ayrıntılarını içeren tüketicilere (kiracılar) bir belge kullanılabilir hale getirir.

**Kimlik**: 0960.09 Scspkuruluş. 1-09. s **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[CORS, her kaynağın Işlev uygulamalarınıza erişmesine izin vermemelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0820b7b9-23aa-4725-a1ce-ae4558f718e5) |Çıkış noktaları arası kaynak paylaşımı (CORS), tüm etki alanlarının Işlev uygulamanıza erişmesine izin vermemelidir. Yalnızca gerekli etki alanlarının Işlev uygulamanızla etkileşime girmesine izin verin. |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RestrictCORSAccess_FuntionApp_Audit.json) |

### <a name="personnel-are-appropriately-trained-on-leading-principles-and-practices-for-all-types-of-information-exchange-oral-paper-and-electronic"></a>Personel, her türlü bilgi değişimi (Oral, kağıt ve elektronik) için önde gelen ilkeler ve uygulamalar üzerinde uygun şekilde eğitilir.

**Kimlik**: 1325.09 s1organizational. 3-09. s **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Işlev uygulamaları için uzaktan hata ayıklama kapatılmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e60b895-3786-45da-8377-9c6b4b6ac5f9) |Uzaktan hata ayıklama, işlev uygulamalarında gelen bağlantı noktalarının açılmasını gerektirir. Uzaktan hata ayıklama kapalı olmalıdır. |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_FunctionApp_Audit.json) |

## <a name="on-line-transactions"></a>Satır içi Işlemler

### <a name="data-involved-in-electronic-commerce-and-online-transactions-is-checked-to-determine-if-it-contains-covered-information"></a>Elektronik Ticaret ve çevrimiçi işlemlerde yer alan veriler, kapsanan bilgileri içerip içermediğini belirlemekte denetlenir.

**Kimlik**: 0943.09 y1organizational. 1-09. y **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Depolama hesaplarına güvenli aktarım etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |Depolama hesabınızda güvenli aktarım gereksinimini denetleyin. Güvenli aktarım, depolama hesabınızı yalnızca güvenli bağlantılardan (HTTPS) istekleri kabul edecek şekilde zorlayan bir seçenektir. HTTPS kullanımı, sunucu ile hizmet arasında kimlik doğrulaması sağlar ve geçiş sırasında ortadaki adam, gizlice dinleme ve oturum ele geçirme gibi ağ katmanı saldırılarına karşı verileri korur |Denetim, reddetme, devre dışı |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |

### <a name="protocols-used-to-communicate-between-all-involved-parties-are-secured-using-cryptographic-techniques-eg-ssl"></a>Tüm dahil edilen taraflar arasında iletişim kurmak için kullanılan protokoller, şifreleme teknikleri (ör. SSL) kullanılarak güvenli hale getirilir.

**Kimlik**: 0945.09 y1organizational. 3-09. y **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Güvenilen kökte belirtilen sertifikaları içermeyen Windows makinelerini denetle](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F934345e1-4dfb-4c70-90d7-41990dc9608b) |Önkoşulların ilke atama kapsamına dağıtılmasını gerektirir. Ayrıntılar için, adresini ziyaret edin [https://aka.ms/gcpol](https://aka.ms/gcpol) . Makine Güvenilen kök sertifika deposu (CERT: \ localmachıneroot), ilke parametresiyle listelenen bir veya daha fazla sertifikayı içermiyorsa makineler uyumlu değildir. |Auditınotexists |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsCertificateInTrustedRoot_AINE.json) |

### <a name="the-organization-requires-the-use-of-encryption-between-and-the-use-of-electronic-signatures-by-each-of-the-parties-involved-in-the-transaction"></a>Kuruluş, işlem içinde yer alan tarafların her biri ile elektronik imzaların kullanımı arasında şifreleme kullanımını gerektirir.

**Kimlik**: 0946.09 y2organizational. 14-09. y **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Redsıs için yalnızca Azure önbelleğinize güvenli bağlantılar etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Redsıs için yalnızca SSL ile SSL aracılığıyla bağlantıların etkinleştirilmesini denetleme. Güvenli bağlantı kullanımı, sunucu ve hizmet arasında kimlik doğrulaması sağlar ve geçiş sırasında ortadaki adam, gizlice dinleme ve oturum ele geçirme gibi ağ katmanı saldırılarına karşı verileri korur |Denetim, reddetme, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

### <a name="the-organization-ensures-the-storage-of-the-transaction-details-are-located-outside-of-any-publicly-accessible-environments-eg-on-a-storage-platform-existing-on-the-organizations-intranet-and-not-retained-and-exposed-on-a-storage-medium-directly-accessible-from-the-internet"></a>Kuruluş, işlem ayrıntılarının depolamanın, genel olarak erişilebilen herhangi bir ortamın dışında (örneğin, kuruluşun intraneti üzerinde var olan bir depolama platformunda) yer almasını ve korunmamasını ve doğrudan Internet 'ten erişilebilen bir depolama ortamına açık olmamasını sağlar.

**Kimlik**: 0947.09 y2organizational. 2-09. y **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[PostgreSQL veritabanı sunucuları için SSL bağlantısını zorla etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |PostgreSQL için Azure veritabanı, PostgreSQL için Azure veritabanı sunucunuzu Güvenli Yuva Katmanı (SSL) kullanarak istemci uygulamalarına bağlamayı destekler. Veritabanı sunucunuz ile istemci uygulamalarınız arasında SSL bağlantılarını zorunlu tutma, sunucu ile uygulamanız arasındaki veri akışını şifreleyerek, ortadaki adam saldırılarına karşı korunmaya yardımcı olur. Bu yapılandırma, veritabanı sunucunuza erişmek için SSL 'nin her zaman etkinleştirilmesini zorunlu kılar. |Denetim, devre dışı |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |

### <a name="where-a-trusted-authority-is-used-eg-for-the-purposes-of-issuing-and-maintaining-digital-signatures-andor-digital-certificates-security-is-integrated-and-embedded-throughout-the-entire-end-to-end-certificatesignature-management-process"></a>Güvenilen bir yetkilinin kullanıldığı yer (örneğin, dijital imzaları ve/veya dijital sertifikaları verme ve sürdürme amacıyla), güvenlik, uçtan uca sertifika/imza yönetimi işleminin tamamına tümleştirilir ve katıştırılır.

**Kimlik**: 0948.09 y2organizational. 3-09. y **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[MySQL veritabanı sunucuları için SSL bağlantısını zorla etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |MySQL için Azure veritabanı, MySQL Server için Azure veritabanı sunucunuzu Güvenli Yuva Katmanı (SSL) kullanarak istemci uygulamalarına bağlamayı destekler. Veritabanı sunucunuz ile istemci uygulamalarınız arasında SSL bağlantılarını zorunlu tutma, sunucu ile uygulamanız arasındaki veri akışını şifreleyerek, ortadaki adam saldırılarına karşı korunmaya yardımcı olur. Bu yapılandırma, veritabanı sunucunuza erişmek için SSL 'nin her zaman etkinleştirilmesini zorunlu kılar. |Denetim, devre dışı |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |

### <a name="the-protocols-used-for-communications-are-enhanced-to-address-any-new-vulnerability-and-the-updated-versions-of-the-protocols-are-adopted-as-soon-as-possible"></a>İletişim için kullanılan protokoller yeni güvenlik açıklarını gidermek üzere geliştirilmiştir ve protokollerin güncelleştirilmiş sürümleri mümkün olan en kısa sürede benimsenmiştir.

**Kimlik**: 0949.09 y2organizational. 5-09. y **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[API uygulaması yalnızca HTTPS üzerinden erişilebilir olmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |HTTPS kullanımı, sunucu/hizmet kimlik doğrulamasını sağlar ve ağ katmanı gizlice dinleme saldırılarına karşı geçiş sırasında verileri korur. |Denetim, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |
|[İşlev Uygulaması yalnızca HTTPS üzerinden erişilebilir olmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |HTTPS kullanımı, sunucu/hizmet kimlik doğrulamasını sağlar ve ağ katmanı gizlice dinleme saldırılarına karşı geçiş sırasında verileri korur. |Denetim, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |
|[API uygulamanızda en son TLS sürümü kullanılmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |En son TLS sürümüne yükselt |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[İşlev Uygulaması en son TLS sürümü kullanılmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |En son TLS sürümüne yükselt |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[Web uygulamanızda en son TLS sürümü kullanılmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |En son TLS sürümüne yükselt |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |
|[Web uygulaması yalnızca HTTPS üzerinden erişilebilir olmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |HTTPS kullanımı, sunucu/hizmet kimlik doğrulamasını sağlar ve ağ katmanı gizlice dinleme saldırılarına karşı geçiş sırasında verileri korur. |Denetim, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

## <a name="control-of-operational-software"></a>Işletimsel yazılımın denetimi

### <a name="only-authorized-administrators-are-allowed-to-implement-approved-upgrades-to-software-applications-and-program-libraries-based-on-business-requirements-and-the-security-implications-of-the-release"></a>Yalnızca yetkili yöneticilerin, iş gereksinimlerine ve yayının güvenlik etkilerine bağlı olarak yazılım, uygulama ve program kitaplıklarına onaylanan yükseltmeler uygulamasına izin verilir.

**Kimlik**: 0605.10 h1system. 12-10. h **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Makinelerinizdeki güvenlik yapılandırmasındaki güvenlik açıkları düzeltilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1e5fd5d-3e4c-4ce1-8661-7d1873ae6b15) |Yapılandırılmış temeli karşılamayan sunucular, Azure Güvenlik Merkezi tarafından öneriler olarak izlenir |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OSVulnerabilities_Audit.json) |
|[Windows makineleri ' güvenlik seçenekleri-denetim ' için gereksinimleri karşılamalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F33936777-f2ac-45aa-82ec-07958ec9ade4) |Windows makineleri denetim ilkesi alt kategorisini zorlamak için ' güvenlik seçenekleri-Audit ' kategorisinde belirtilen grup ilkesi ayarlara sahip olmalıdır ve güvenlik denetimleri günlüğe kaydedilemezse kapanıyor. Bu ilke, Konuk yapılandırma ön koşullarının ilke atama kapsamına dağıtılmasını gerektirir. Ayrıntılar için, adresini ziyaret edin [https://aka.ms/gcpol](https://aka.ms/gcpol) . |Auditınotexists, devre dışı |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecurityOptionsAudit_AINE.json) |
|[Windows makineleri ' sistem denetim Ilkeleri-hesap yönetimi ' için gereksinimleri karşılamalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F94d9aca8-3757-46df-aa51-f218c5f11954) |Windows makineleri, uygulama, güvenlik ve Kullanıcı grubu yönetimi ve diğer yönetim olaylarını denetlemek için ' sistem denetim Ilkeleri-hesap yönetimi ' kategorisinde belirtilen grup ilkesi ayarlara sahip olmalıdır. Bu ilke, Konuk yapılandırma ön koşullarının ilke atama kapsamına dağıtılmasını gerektirir. Ayrıntılar için, adresini ziyaret edin [https://aka.ms/gcpol](https://aka.ms/gcpol) . |Auditınotexists, devre dışı |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesAccountManagement_AINE.json) |

### <a name="applications-and-operating-systems-are-successfully-tested-for-usability-security-and-impact-prior-to-production"></a>Uygulamalar ve işletim sistemleri, üretim öncesinde kullanılabilirlik, güvenlik ve etki açısından başarıyla test edilmiştir.

**Kimlik**: 0606.10 h2system. 1-10. h **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Kapsayıcı güvenlik yapılandırmalarında güvenlik açıkları düzeltilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8cbc669-f12d-49eb-93e7-9273119e9933) |Docker yüklü makinelerde güvenlik yapılandırmasındaki güvenlik açıklarını giderir ve Azure Güvenlik Merkezi 'nde öneri olarak görüntülenir. |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ContainerBenchmark_Audit.json) |

### <a name="the-organization-uses-its-configuration-control-program-to-maintain-control-of-all-implemented-software-and-its-system-documentation-and-archive-prior-versions-of-implemented-software-and-associated-system-documentation"></a>Kuruluş, uygulanan tüm yazılımların ve sistem belgelerinin denetimini sürdürmek ve uygulanan yazılımın ve ilişkili sistem belgelerinin önceki sürümlerini arşivlemek için yapılandırma denetim programını kullanır.

**Kimlik**: 0607.10 h2system. 23-10. h **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Güvenli uygulamaları tanımlamaya yönelik Uyarlamalı uygulama denetimleri, makinelerinizde etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |Makinelerinizde çalışan bilinen güvenli uygulamaların listesini tanımlamak için uygulama denetimlerini etkinleştirin ve diğer uygulamalar çalıştırıldığında sizi uyarır. Bu, makinelerinizi kötü amaçlı yazılımlara karşı korumanıza yardımcı olur. Güvenlik Merkezi, kurallarınızı yapılandırma ve sürdürme sürecini basitleştirmek için makine öğrenimini kullanarak her makinede çalışan uygulamaları analiz eder ve bilinen güvenli uygulamalar listesini önerir. |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |
|[Sanal makine ölçek kümelerinizin güvenlik yapılandırmasındaki güvenlik açıkları düzeltilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3c735d8a-a4ba-4a3a-b7cf-db7754cf57f4) |Saldırılara karşı korumak için sanal makine ölçek kümelerinizin üzerindeki işletim sistemi güvenlik açıklarını denetleyin. |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssOSVulnerabilities_Audit.json) |

## <a name="change-control-procedures"></a>Denetim yordamlarını değiştirme

### <a name="managers-responsible-for-application-systems-are-also-responsible-for-the-strict-control-security-of-the-project-or-support-environment-and-ensure-that-all-proposed-system-changes-are-reviewed-to-check-that-they-do-not-compromise-the-security-of-either-the-system-or-the-operating-environment"></a>Uygulama sistemlerinden sorumlu Yöneticiler ayrıca, proje veya destek ortamının katı denetiminden (güvenlik) sorumludur ve sistemin ya da işletim ortamının güvenliğine güvenmemesini denetlemek için önerilen tüm sistem değişikliklerinin gözden geçirilmesini sağlar.

**Kimlik**: 0635.10 k1organizational. 12-10. k **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Windows makineleri ' sistem denetim Ilkeleri-ayrıntılı Izleme ' için gereksinimleri karşılamalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Windows makineleri, DPAPI, işlem oluşturma/sonlandırma, RPC olayları ve PNP etkinliğini denetlemek için ' sistem denetim Ilkeleri-ayrıntılı Izleme ' kategorisinde belirtilen grup ilkesi ayarlara sahip olmalıdır. Bu ilke, Konuk yapılandırma ön koşullarının ilke atama kapsamına dağıtılmasını gerektirir. Ayrıntılar için, adresini ziyaret edin [https://aka.ms/gcpol](https://aka.ms/gcpol) . |Auditınotexists, devre dışı |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="the-organization-formally-addresses-purpose-scope-roles-responsibilities-management-commitment-coordination-among-organizational-entities-and-compliance-for-configuration-management-eg-through-policies-standards-processes"></a>Kuruluş, amaç, kapsam, roller, sorumluluklar, yönetim taahhüdünü, kurumsal varlıklar arasında koordinasyon ve yapılandırma yönetimi için uyumluluğu (örneğin, ilkeler, standartlar, süreçler aracılığıyla) bir şekilde ele alınmaktadır.

**Kimlik**: 0636.10 k2organizational. 1-10. k **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Windows makineleri ' sistem denetim Ilkeleri-ayrıntılı Izleme ' için gereksinimleri karşılamalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Windows makineleri, DPAPI, işlem oluşturma/sonlandırma, RPC olayları ve PNP etkinliğini denetlemek için ' sistem denetim Ilkeleri-ayrıntılı Izleme ' kategorisinde belirtilen grup ilkesi ayarlara sahip olmalıdır. Bu ilke, Konuk yapılandırma ön koşullarının ilke atama kapsamına dağıtılmasını gerektirir. Ayrıntılar için, adresini ziyaret edin [https://aka.ms/gcpol](https://aka.ms/gcpol) . |Auditınotexists, devre dışı |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="the-organization-has-developed-documented-and-implemented-a-configuration-management-plan-for-the-information-system"></a>Kuruluş, bilgi sistemi için bir yapılandırma yönetimi planı geliştirmiştir, belgelenmiştir ve uygulamıştır.

**Kimlik**: 0637.10 k2organizational. 2-10. k **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Windows makineleri ' sistem denetim Ilkeleri-ayrıntılı Izleme ' için gereksinimleri karşılamalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Windows makineleri, DPAPI, işlem oluşturma/sonlandırma, RPC olayları ve PNP etkinliğini denetlemek için ' sistem denetim Ilkeleri-ayrıntılı Izleme ' kategorisinde belirtilen grup ilkesi ayarlara sahip olmalıdır. Bu ilke, Konuk yapılandırma ön koşullarının ilke atama kapsamına dağıtılmasını gerektirir. Ayrıntılar için, adresini ziyaret edin [https://aka.ms/gcpol](https://aka.ms/gcpol) . |Auditınotexists, devre dışı |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="changes-are-formally-controlled-documented-and-enforced-in-order-to-minimize-the-corruption-of-information-systems"></a>Bilgi sistemlerinin bozulmasını en aza indirmek için değişiklikler resmi olarak denetlenir, belgelenmiştir ve zorlanır.

**Kimlik**: 0638.10 k2organizational. 34569-10. k **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Windows makineleri ' sistem denetim Ilkeleri-ayrıntılı Izleme ' için gereksinimleri karşılamalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Windows makineleri, DPAPI, işlem oluşturma/sonlandırma, RPC olayları ve PNP etkinliğini denetlemek için ' sistem denetim Ilkeleri-ayrıntılı Izleme ' kategorisinde belirtilen grup ilkesi ayarlara sahip olmalıdır. Bu ilke, Konuk yapılandırma ön koşullarının ilke atama kapsamına dağıtılmasını gerektirir. Ayrıntılar için, adresini ziyaret edin [https://aka.ms/gcpol](https://aka.ms/gcpol) . |Auditınotexists, devre dışı |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="installation-checklists-and-vulnerability-scans-are-used-to-validate-the-configuration-of-servers-workstations-devices-and-appliances-and-ensure-the-configuration-meets-minimum-standards"></a>Yükleme denetim listeleri ve güvenlik açığı taramaları, sunucuların, iş istasyonlarının, cihazların ve gereçlerinin yapılandırılmasını doğrulamak ve yapılandırmanın en düşük standartları karşıladığından emin olmak için kullanılır.

**Kimlik**: 0639.10 k2organizational. 78-10. k **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Windows makineleri ' sistem denetim Ilkeleri-ayrıntılı Izleme ' için gereksinimleri karşılamalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Windows makineleri, DPAPI, işlem oluşturma/sonlandırma, RPC olayları ve PNP etkinliğini denetlemek için ' sistem denetim Ilkeleri-ayrıntılı Izleme ' kategorisinde belirtilen grup ilkesi ayarlara sahip olmalıdır. Bu ilke, Konuk yapılandırma ön koşullarının ilke atama kapsamına dağıtılmasını gerektirir. Ayrıntılar için, adresini ziyaret edin [https://aka.ms/gcpol](https://aka.ms/gcpol) . |Auditınotexists, devre dışı |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="where-development-is-outsourced-change-control-procedures-to-address-security-are-included-in-the-contracts-and-specifically-require-the-developer-to-track-security-flaws-and-flaw-resolution-within-the-system-component-or-service-and-report-findings-to-organization-defined-personnel-or-roles"></a>Geliştirme için dış kaynak olarak değişiklik denetim yordamlarını sözleşmeye dahil edin ve özellikle geliştiricinin sistem, bileşen ya da hizmet içinde güvenlik kusurlarını ve hata çözümlemesini izlemesini, böylece geliştiricilerin da kuruluş tarafından tanımlanan personeli veya rolleri nasıl bildirmesini gerektirir.

**Kimlik**: 0640.10 k2organizational. 1012-10. k **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Windows makineleri ' sistem denetim Ilkeleri-ayrıntılı Izleme ' için gereksinimleri karşılamalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Windows makineleri, DPAPI, işlem oluşturma/sonlandırma, RPC olayları ve PNP etkinliğini denetlemek için ' sistem denetim Ilkeleri-ayrıntılı Izleme ' kategorisinde belirtilen grup ilkesi ayarlara sahip olmalıdır. Bu ilke, Konuk yapılandırma ön koşullarının ilke atama kapsamına dağıtılmasını gerektirir. Ayrıntılar için, adresini ziyaret edin [https://aka.ms/gcpol](https://aka.ms/gcpol) . |Auditınotexists, devre dışı |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="the-organization-does-not-use-automated-updates-on-critical-systems"></a>Kuruluş, kritik sistemlerde Otomatik Güncelleştirmeler kullanmaz.

**Kimlik**: 0641.10 k2organizational. 11-10. k **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Windows makineleri ' sistem denetim Ilkeleri-ayrıntılı Izleme ' için gereksinimleri karşılamalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Windows makineleri, DPAPI, işlem oluşturma/sonlandırma, RPC olayları ve PNP etkinliğini denetlemek için ' sistem denetim Ilkeleri-ayrıntılı Izleme ' kategorisinde belirtilen grup ilkesi ayarlara sahip olmalıdır. Bu ilke, Konuk yapılandırma ön koşullarının ilke atama kapsamına dağıtılmasını gerektirir. Ayrıntılar için, adresini ziyaret edin [https://aka.ms/gcpol](https://aka.ms/gcpol) . |Auditınotexists, devre dışı |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="the-organization-develops-documents-and-maintains-under-configuration-control-a-current-baseline-configuration-of-the-information-system-and-reviews-and-updates-the-baseline-as-required"></a>Kuruluş, yapılandırma denetimi altında, bilgi sisteminin geçerli bir temel yapılandırması olan, belgeler ve bakımını geliştirir ve temeli gerektiği şekilde inceler ve güncelleştirir.

**Kimlik**: 0642.10 k3organizational. 12-10. k **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Windows makineleri ' sistem denetim Ilkeleri-ayrıntılı Izleme ' için gereksinimleri karşılamalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Windows makineleri, DPAPI, işlem oluşturma/sonlandırma, RPC olayları ve PNP etkinliğini denetlemek için ' sistem denetim Ilkeleri-ayrıntılı Izleme ' kategorisinde belirtilen grup ilkesi ayarlara sahip olmalıdır. Bu ilke, Konuk yapılandırma ön koşullarının ilke atama kapsamına dağıtılmasını gerektirir. Ayrıntılar için, adresini ziyaret edin [https://aka.ms/gcpol](https://aka.ms/gcpol) . |Auditınotexists, devre dışı |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="the-organization-i-establishes-and-documents-mandatory-configuration-settings-for-information-technology-products-employed-within-the-information-system-using-the-latest-security-configuration-baselines-ii-identifies-documents-and-approves-exceptions-from-the-mandatory-established-configuration-settings-for-individual-components-based-on-explicit-operational-requirements-and-iii-monitors-and-controls-changes-to-the-configuration-settings-in-accordance-with-organizational-policies-and-procedures"></a>Kuruluş (i), en son güvenlik yapılandırması temellerini kullanarak bilgi sistemi içinde çalışan bilgi teknolojisi ürünlerinin zorunlu yapılandırma ayarlarını belirler ve belgeler. (ii) açık işletimsel gereksinimlere göre tek tek bileşenler için zorunlu olarak belirlenmiş yapılandırma ayarlarından özel durumları tanımlar, belgeler ve onaylar. ve (iii), kuruluş ilkelerine ve yordamlarına uygun olarak yapılandırma ayarlarındaki değişiklikleri izler ve denetler.

**Kimlik**: 0643.10 k3organizational. 3-10. k **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Windows makineleri ' sistem denetim Ilkeleri-ayrıntılı Izleme ' için gereksinimleri karşılamalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Windows makineleri, DPAPI, işlem oluşturma/sonlandırma, RPC olayları ve PNP etkinliğini denetlemek için ' sistem denetim Ilkeleri-ayrıntılı Izleme ' kategorisinde belirtilen grup ilkesi ayarlara sahip olmalıdır. Bu ilke, Konuk yapılandırma ön koşullarının ilke atama kapsamına dağıtılmasını gerektirir. Ayrıntılar için, adresini ziyaret edin [https://aka.ms/gcpol](https://aka.ms/gcpol) . |Auditınotexists, devre dışı |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="the-organization-employs-automated-mechanisms-to-i-centrally-manage-apply-and-verify-configuration-settings-ii-respond-to-unauthorized-changes-to-network-and-system-security-related-configuration-settings-and-iii-enforce-access-restrictions-and-auditing-of-the-enforcement-actions"></a>Kuruluş, (i) yapılandırma ayarlarını merkezi olarak yönetmek, uygulamak ve doğrulamak için otomatik mekanizmalar kullanır; (ii) ağ ve sistemle güvenlikle ilgili yapılandırma ayarlarındaki yetkisiz değişikliklere yanıt verir; ve (iii) uygulama eylemlerinin erişim kısıtlamalarını ve denetimini uygular.

**Kimlik**: 0644.10 k3organizational. 4-10. k **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Windows makineleri ' sistem denetim Ilkeleri-ayrıntılı Izleme ' için gereksinimleri karşılamalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Windows makineleri, DPAPI, işlem oluşturma/sonlandırma, RPC olayları ve PNP etkinliğini denetlemek için ' sistem denetim Ilkeleri-ayrıntılı Izleme ' kategorisinde belirtilen grup ilkesi ayarlara sahip olmalıdır. Bu ilke, Konuk yapılandırma ön koşullarının ilke atama kapsamına dağıtılmasını gerektirir. Ayrıntılar için, adresini ziyaret edin [https://aka.ms/gcpol](https://aka.ms/gcpol) . |Auditınotexists, devre dışı |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

## <a name="control-of-technical-vulnerabilities"></a>Teknik güvenlik açıklarının denetimi

### <a name="technical-vulnerabilities-are-identified-evaluated-for-risk-and-corrected-in-a-timely-manner"></a>Teknik güvenlik açıkları tanımlanır, risk için değerlendirilir ve zamanında düzeltilir.

**Kimlik**: 0709.10 m1organizational. 1-10. d **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Sanal makinelerinizde bir güvenlik açığı değerlendirme çözümünün etkinleştirilmesi gerekir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F501541f7-f7e7-4cd6-868c-4190fdad3ac9) |Sanal makineleri, desteklenen bir güvenlik açığı değerlendirme çözümü çalıştırıp çalıştırmadığını tespit etmek üzere denetler. Her bir siber risk ve güvenlik programının çekirdek bileşeni, güvenlik açıklarının tanımlama ve analizidir. Azure Güvenlik Merkezi 'nin standart fiyatlandırma katmanı, sanal makineleriniz için ek ücret olmadan güvenlik açığı taraması içerir. Ayrıca, güvenlik merkezi bu aracı sizin için otomatik olarak dağıtabilir. |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ServerVulnerabilityAssessment_Audit.json) |
|[Kapsayıcı güvenlik yapılandırmalarında güvenlik açıkları düzeltilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8cbc669-f12d-49eb-93e7-9273119e9933) |Docker yüklü makinelerde güvenlik yapılandırmasındaki güvenlik açıklarını giderir ve Azure Güvenlik Merkezi 'nde öneri olarak görüntülenir. |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ContainerBenchmark_Audit.json) |
|[Makinelerinizdeki güvenlik yapılandırmasındaki güvenlik açıkları düzeltilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1e5fd5d-3e4c-4ce1-8661-7d1873ae6b15) |Yapılandırılmış temeli karşılamayan sunucular, Azure Güvenlik Merkezi tarafından öneriler olarak izlenir |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OSVulnerabilities_Audit.json) |
|[Sanal makine ölçek kümelerinizin güvenlik yapılandırmasındaki güvenlik açıkları düzeltilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3c735d8a-a4ba-4a3a-b7cf-db7754cf57f4) |Saldırılara karşı korumak için sanal makine ölçek kümelerinizin üzerindeki işletim sistemi güvenlik açıklarını denetleyin. |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssOSVulnerabilities_Audit.json) |
|[SQL veritabanlarınızdaki güvenlik açıkları düzeltilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffeedbf84-6b99-488c-acc2-71c829aa5ffc) |İzleme güvenlik açığı değerlendirmesi tarama sonuçları ve veritabanı güvenlik açıklarının nasıl düzeltileceğine ilişkin öneriler. |Auditınotexists, devre dışı |[4.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_SQLDbVulnerabilities_Audit.json) |
|[Güvenlik açığı değerlendirmesi SQL yönetilen örneği üzerinde etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1b7aa243-30e4-4c9e-bca8-d0d3022b634a) |Yinelenen güvenlik açığı değerlendirmesi taramaları etkinleştirilmemiş olan her bir SQL yönetilen örneği denetleyin. Güvenlik açığı değerlendirmesi, olası veritabanı güvenlik açıklarını düzeltmenizi, izleyebilir ve yardımcı olabilir. |Auditınotexists, devre dışı |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnManagedInstance_Audit.json) |
|[Güvenlik açığı değerlendirmesi SQL sunucularınızda etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef2a8f2a-b3d9-49cd-a8a8-9a3aaaf647d9) |Yinelenen güvenlik açığı değerlendirmesi taramaları etkinleştirilmemiş olan Azure SQL Server 'ı denetleyin. Güvenlik açığı değerlendirmesi, olası veritabanı güvenlik açıklarını düzeltmenizi, izleyebilir ve yardımcı olabilir. |Auditınotexists, devre dışı |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnServer_Audit.json) |
|[Windows makineleri, ' güvenlik seçenekleri-Microsoft ağ sunucusu ' gereksinimlerini karşılamalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcaf2d518-f029-4f6b-833b-d7081702f253) |Windows makineleri SMB v1 sunucusunu devre dışı bırakmak için ' güvenlik seçenekleri-Microsoft ağ sunucusu ' kategorisinde belirtilen grup ilkesi ayarlara sahip olmalıdır. Bu ilke, Konuk yapılandırma ön koşullarının ilke atama kapsamına dağıtılmasını gerektirir. Ayrıntılar için, adresini ziyaret edin [https://aka.ms/gcpol](https://aka.ms/gcpol) . |Auditınotexists, devre dışı |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecurityOptionsMicrosoftNetworkServer_AINE.json) |

### <a name="a-hardened-configuration-standard-exists-for-all-system-and-network-components"></a>Tüm sistem ve ağ bileşenleri için sağlamlaştırılmış bir yapılandırma standardı vardır.

**Kimlik**: 0710.10 m2organizational. 1-10. d **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Güvenlik açığı değerlendirmesi SQL yönetilen örneği üzerinde etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1b7aa243-30e4-4c9e-bca8-d0d3022b634a) |Yinelenen güvenlik açığı değerlendirmesi taramaları etkinleştirilmemiş olan her bir SQL yönetilen örneği denetleyin. Güvenlik açığı değerlendirmesi, olası veritabanı güvenlik açıklarını düzeltmenizi, izleyebilir ve yardımcı olabilir. |Auditınotexists, devre dışı |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnManagedInstance_Audit.json) |

### <a name="a-technical-vulnerability-management-program-is-in-place-to-monitor-assess-rank-and-remediate-vulnerabilities-identified-in-systems"></a>Sistemlerde tanımlanan güvenlik açıklarını izlemek, değerlendirmek, derecelendirmek ve düzeltmek için teknik bir güvenlik açığı yönetim programı vardır.

**Kimlik**: 0711.10 m2organizational. 23-10. d **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Sanal makinelerinizde bir güvenlik açığı değerlendirme çözümünün etkinleştirilmesi gerekir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F501541f7-f7e7-4cd6-868c-4190fdad3ac9) |Sanal makineleri, desteklenen bir güvenlik açığı değerlendirme çözümü çalıştırıp çalıştırmadığını tespit etmek üzere denetler. Her bir siber risk ve güvenlik programının çekirdek bileşeni, güvenlik açıklarının tanımlama ve analizidir. Azure Güvenlik Merkezi 'nin standart fiyatlandırma katmanı, sanal makineleriniz için ek ücret olmadan güvenlik açığı taraması içerir. Ayrıca, güvenlik merkezi bu aracı sizin için otomatik olarak dağıtabilir. |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ServerVulnerabilityAssessment_Audit.json) |

### <a name="patches-are-tested-and-evaluated-before-they-are-installed"></a>Düzeltme ekleri, yüklenmeden önce test edilir ve değerlendirilir.

**Kimlik**: 0713.10 m2organizational. 5-10. d **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Makinelerinizdeki güvenlik yapılandırmasındaki güvenlik açıkları düzeltilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1e5fd5d-3e4c-4ce1-8661-7d1873ae6b15) |Yapılandırılmış temeli karşılamayan sunucular, Azure Güvenlik Merkezi tarafından öneriler olarak izlenir |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OSVulnerabilities_Audit.json) |

### <a name="the-technical-vulnerability-management-program-is-evaluated-on-a-quarterly-basis"></a>Teknik güvenlik açığı yönetimi programı üç aylık olarak değerlendirilir.

**Kimlik**: 0714.10 m2organizational. 7-10. d **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Sanal makine ölçek kümelerinizin güvenlik yapılandırmasındaki güvenlik açıkları düzeltilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3c735d8a-a4ba-4a3a-b7cf-db7754cf57f4) |Saldırılara karşı korumak için sanal makine ölçek kümelerinizin üzerindeki işletim sistemi güvenlik açıklarını denetleyin. |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssOSVulnerabilities_Audit.json) |

### <a name="systems-are-appropriately-hardened-eg-configured-with-only-necessary-and-secure-services-ports-and-protocols-enabled"></a>Sistemler uygun şekilde sağlamlaştırılmış (ör. yalnızca gerekli ve güvenli hizmetler, bağlantı noktaları ve protokoller etkinleştirilmiş şekilde yapılandırılır).

**Kimlik**: 0715.10 m2organizational. 8-10. d **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Kapsayıcı güvenlik yapılandırmalarında güvenlik açıkları düzeltilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8cbc669-f12d-49eb-93e7-9273119e9933) |Docker yüklü makinelerde güvenlik yapılandırmasındaki güvenlik açıklarını giderir ve Azure Güvenlik Merkezi 'nde öneri olarak görüntülenir. |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ContainerBenchmark_Audit.json) |

### <a name="the-organization-conducts-an-enterprise-security-posture-review-as-needed-but-no-less-than-once-within-every-three-hundred-sixty-five-365-days-in-accordance-with-organizational-is-procedures"></a>Kuruluş, kurumsal güvenlik duruşunu gereken şekilde gözden geçirir ancak her 365 (365) günde bir kereden fazla değil, kuruluşa göre yordamlara göre daha az olmaz.

**Kimlik**: 0716.10 m3organizational. 1-10. d **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[SQL veritabanlarınızdaki güvenlik açıkları düzeltilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffeedbf84-6b99-488c-acc2-71c829aa5ffc) |İzleme güvenlik açığı değerlendirmesi tarama sonuçları ve veritabanı güvenlik açıklarının nasıl düzeltileceğine ilişkin öneriler. |Auditınotexists, devre dışı |[4.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_SQLDbVulnerabilities_Audit.json) |

### <a name="vulnerability-scanning-tools-include-the-capability-to-readily-update-the-information-system-vulnerabilities-scanned"></a>Güvenlik açığı tarama araçları, taranan bilgi sistemi güvenlik açıklarını kolayca güncelleştirme özelliğini içerir.

**Kimlik**: 0717.10 m3organizational. 2-10. d **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Sanal makine ölçek kümelerinizin güvenlik yapılandırmasındaki güvenlik açıkları düzeltilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3c735d8a-a4ba-4a3a-b7cf-db7754cf57f4) |Saldırılara karşı korumak için sanal makine ölçek kümelerinizin üzerindeki işletim sistemi güvenlik açıklarını denetleyin. |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssOSVulnerabilities_Audit.json) |

### <a name="the-organization-scans-for-vulnerabilities-in-the-information-system-and-hosted-applications-to-determine-the-state-of-flaw-remediation-monthly-automatically-and-again-manually-or-automatically-when-new-vulnerabilities-potentially-affecting-the-systems-and-networked-environments-are-identified-and-reported"></a>Kuruluş, bilgi sistemi ve barındırılan uygulamalarda güvenlik açıklarını tarar ve bu durumda sistemleri etkileyen yeni güvenlik açıkları ve ağa bağlı ortamlar tanımlanır ve raporlanır.

**Kimlik**: 0718.10 m3organizational. 34-10. d **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Makinelerinizdeki güvenlik yapılandırmasındaki güvenlik açıkları düzeltilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1e5fd5d-3e4c-4ce1-8661-7d1873ae6b15) |Yapılandırılmış temeli karşılamayan sunucular, Azure Güvenlik Merkezi tarafından öneriler olarak izlenir |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OSVulnerabilities_Audit.json) |

### <a name="the-organization-updates-the-list-of-information-system-vulnerabilities-scanned-within-every-thirty-30-days-or-when-new-vulnerabilities-are-identified-and-reported"></a>Kuruluş, her otuz (30) günde bir taranan bilgi sistemi güvenlik açıkları listesini veya yeni güvenlik açıkları tanımlandığında ve bildirilmek üzere güncelleştirilir.

**Kimlik**: 0719.10 m3organizational. 5-10. d **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Güvenlik açığı değerlendirmesi SQL yönetilen örneği üzerinde etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1b7aa243-30e4-4c9e-bca8-d0d3022b634a) |Yinelenen güvenlik açığı değerlendirmesi taramaları etkinleştirilmemiş olan her bir SQL yönetilen örneği denetleyin. Güvenlik açığı değerlendirmesi, olası veritabanı güvenlik açıklarını düzeltmenizi, izleyebilir ve yardımcı olabilir. |Auditınotexists, devre dışı |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnManagedInstance_Audit.json) |

## <a name="business-continuity-and-risk-assessment"></a>İş sürekliliği ve risk değerlendirmesi

### <a name="the-organization-identifies-the-critical-business-processes-requiring-business-continuity"></a>Kuruluş, iş sürekliliği gerektiren kritik iş süreçlerini tanımlar.

**Kimlik**: 1634.12 b1organizational. 1-12. b **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Olağanüstü durum kurtarma yapılandırması olmadan sanal makineleri denetleme](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0015ea4d-51ff-4ce3-8d8c-f3f8f0179a56) |Olağanüstü durum kurtarma yapılandırması olmayan sanal makineleri denetleyin. Olağanüstü durum kurtarma hakkında daha fazla bilgi edinmek için adresini ziyaret edin [https://aka.ms/asr-doc](https://aka.ms/asr-doc) . |Auditınotexists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/RecoveryServices_DisasterRecovery_Audit.json) |

### <a name="information-security-aspects-of-business-continuity-are-i-based-on-identifying-events-or-sequence-of-events-that-can-cause-interruptions-to-the-organizations-critical-business-processes-eg-equipment-failure-human-errors-theft-fire-natural-disasters-acts-of-terrorism-ii-followed-by-a-risk-assessment-to-determine-the-probability-and-impact-of-such-interruptions-in-terms-of-time-damage-scale-and-recovery-period-iii-based-on-the-results-of-the-risk-assessment-a-business-continuity-strategy-is-developed-to-identify-the-overall-approach-to-business-continuity-and-iv-once-this-strategy-has-been-created-endorsement-is-provided-by-management-and-a-plan-created-and-endorsed-to-implement-this-strategy"></a>İş sürekliliği 'nin bilgi güvenliği yönleri, kuruluşun kritik iş süreçlerine (örneğin, ekipman arızası, insan hataları, hırsızlık, ateş, doğal felate, Terrorism 'nin) kesintiye neden olabilecek olayları (veya olay sırasını) tanımlamaya göre (i). (ii) ve bu kesintileri, zaman içinde, hasar ve kurtarma dönemi açısından, bu kesintileri olasılığa ve etkisini tespit etmek için bir risk değerlendirmesi. (III) risk değerlendirmesi sonuçlarına göre, iş sürekliliği için genel yaklaşımı belirlemek üzere bir iş sürekliliği stratejisi geliştirilmiştir; ve (IV) Bu strateji oluşturulduktan sonra, yönetim tarafından onay sağlanır ve bu stratejiyi uygulamak için oluşturulup onaylanan bir plan oluşturulur.

**Kimlik**: 1635.12 b1organizational. 2-12. b **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Azure Key Vault Managed HSM 'nin Temizleme koruması etkin olmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc39ba22d-4428-4149-b981-70acb31fc383) |Azure Key Vault yönetilen bir HSM 'nin kötü bir şekilde silinmesi kalıcı veri kaybına neden olabilir. Kuruluşunuzda kötü amaçlı bir Insider, yönetilen HSM Azure Key Vault silebilir ve temizedebilir. Temizleme koruması, geçici olarak silinen Azure Key Vault yönetilen HSM için zorunlu bir saklama süresi zorlayarak Insider saldırılarına karşı sizi korur. Kuruluş içinde hiç kimse veya Microsoft, geçici silme Bekletme dönemi sırasında Azure Key Vault yönetilen HSM 'yi temizleyemezsiniz. |Denetim, reddetme, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/ManagedHsm_Recoverable_Audit.json) |
|[Anahtar kasaları Temizleme koruması etkin olmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b60c0b2-2dc2-4e1c-b5c9-abbed971de53) |Bir anahtar kasasının kötü bir şekilde silinmesi kalıcı veri kaybına neden olabilir. Kuruluşunuzda kötü amaçlı bir Insider, anahtar kasalarını silebilir ve temizlerse. Temizleme koruması, geçici olarak silinen Anahtar kasaları için zorunlu bir saklama süresi zorlayarak Insider saldırılarına karşı sizi korur. Kuruluş içinde hiç kimse veya Microsoft, geçici silme Bekletme dönemi sırasında anahtar kasalarınızı temizlenebilir. |Denetim, reddetme, devre dışı |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_Recoverable_Audit.json) |

### <a name="business-impact-analysis-are-used-to-evaluate-the-consequences-of-disasters-security-failures-loss-of-service-and-service-availability"></a>İş etkisi analizi, olağanüstü durumlar, güvenlik sorunları, hizmet kaybı ve hizmet kullanılabilirliği sonuçlarını değerlendirmek için kullanılır.

**Kimlik**: 1637.12 b2organizational. 2-12. b **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Windows makineleri, ' güvenlik seçenekleri-Kurtarma Konsolu ' gereksinimlerini karşılamalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff71be03e-e25b-4d0f-b8bc-9b3e309b66c0) |Windows makineleri, tüm sürücü ve klasörlere disket kopyalama ve erişime izin vermek için ' güvenlik seçenekleri-Kurtarma Konsolu ' kategorisinde belirtilen grup ilkesi ayarlara sahip olmalıdır. Bu ilke, Konuk yapılandırma ön koşullarının ilke atama kapsamına dağıtılmasını gerektirir. Ayrıntılar için, adresini ziyaret edin [https://aka.ms/gcpol](https://aka.ms/gcpol) . |Auditınotexists, devre dışı |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecurityOptionsRecoveryconsole_AINE.json) |

### <a name="business-continuity-risk-assessments-i-are-carried-out-annually-with-full-involvement-from-owners-of-business-resources-and-processes-ii-consider-all-business-processes-and-is-not-limited-to-the-information-assets-but-includes-the-results-specific-to-information-security-and-iii-identifies-quantifies-and-prioritizes-risks-against-key-business-objectives-and-criteria-relevant-to-the-organization-including-critical-resources-impacts-of-disruptions-allowable-outage-times-and-recovery-priorities"></a>İş sürekliliği risk değerlendirmeleri (ı) yıllık olarak iş kaynakları ve süreçlerinden tam katılımıyla yürütülür; (ii) tüm iş süreçlerini düşünün ve bilgi varlıklarıyla sınırlı değildir, ancak bilgi güvenliğine özgü sonuçları içerir; ve (iii), kritik kaynaklar, kesintiler, izin verilen kesinti süreleri ve kurtarma önceliklerinin etkileri dahil olmak üzere kuruluş ile ilgili önemli iş hedeflerine ve ölçütlere karşı riskleri tanımlar, niceler ve önceliklendirir.

**Kimlik**: 1638.12 b2organizational. 345-12. b **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Olağanüstü durum kurtarma yapılandırması olmadan sanal makineleri denetleme](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0015ea4d-51ff-4ce3-8d8c-f3f8f0179a56) |Olağanüstü durum kurtarma yapılandırması olmayan sanal makineleri denetleyin. Olağanüstü durum kurtarma hakkında daha fazla bilgi edinmek için adresini ziyaret edin [https://aka.ms/asr-doc](https://aka.ms/asr-doc) . |Auditınotexists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/RecoveryServices_DisasterRecovery_Audit.json) |

> [!NOTE]
> Belirli Azure Ilke tanımlarının kullanılabilirliği, Azure Kamu ve diğer ulusal bulutlarda farklılık gösterebilir.

## <a name="next-steps"></a>Sonraki adımlar

Azure Ilkesi ile ilgili ek makaleler:

- [Mevzuata uyumluluğuna](../concepts/regulatory-compliance.md) genel bakış.
- [Girişim tanımı yapısına](../concepts/initiative-definition-structure.md)bakın.
- [Azure ilke örneklerinde](./index.md)diğer örnekleri gözden geçirin.
- [İlkenin etkilerini anlama](../concepts/effects.md) konusunu gözden geçirin.
- [Uyumlu olmayan kaynakları nasıl düzelteceğinizi](../how-to/remediate-resources.md)öğrenin.
