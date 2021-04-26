---
title: NıST SP 800-53 R4 için mevzuat uyumluluk ayrıntıları
description: NıST SP 800-53 R4 mevzuatı uyumluluğu yerleşik girişiminin ayrıntıları. Her denetim, değerlendirmede yardımcı olan bir veya daha fazla Azure Ilke tanımına eşlenir.
ms.date: 04/21/2021
ms.topic: sample
ms.custom: generated
ms.openlocfilehash: 01f5890d47d14355484d11e6d31585aff1f3a43d
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107870706"
---
# <a name="details-of-the-nist-sp-800-53-r4-regulatory-compliance-built-in-initiative"></a>NıST SP 800-53 R4 mevzuatı uyumluluğu yerleşik girişiminin ayrıntıları

Aşağıdaki makalede, Azure Ilke mevzuatı uyumluluğu yerleşik girişim tanımının NıST SP 800-53 R4 ' deki **Uyumluluk etki alanları** ve **denetimleriyle** nasıl eşleştiği açıklanır.
Bu uyumluluk standardı hakkında daha fazla bilgi için bkz. [NıST SP 800-53 R4](https://nvd.nist.gov/800-53). _Sahipliği_ anlamak için bkz. [Azure ilke ilkesi tanımı](../concepts/definition-structure.md#type) ve [bulutta paylaşılan sorumluluk](../../../security/fundamentals/shared-responsibility.md).

Aşağıdaki eşlemeler **NıST SP 800-53 R4** denetimlerine göre yapılır. Doğrudan belirli bir **Uyumluluk etki alanına** geçmek için sağ taraftaki gezintiyi kullanın. Denetimlerin birçoğu bir [Azure Policy](../overview.md) Initiative tanımıyla uygulanır. Tüm girişim tanımını gözden geçirmek için Azure portal **ilkeyi** açın ve **tanımlar** sayfasını seçin.
Ardından, **NıST SP 800-53 R4** mevzuat uyumluluğu yerleşik girişim tanımını bulun ve seçin.

Bu yerleşik girişim, [NIST SP 800-53 R4 şema örneğinin](../../blueprints/samples/nist-sp-800-53-r4.md)bir parçası olarak dağıtılır.

> [!IMPORTANT]
> Aşağıdaki her denetim bir veya daha fazla [Azure ilke](../overview.md) tanımı ile ilişkilidir.
> Bu ilkeler, denetimiyle [uyumluluğu değerlendirmenize](../how-to/get-compliance-data.md) yardımcı olabilir; Ancak, bir denetim ile bir veya daha fazla ilke arasında genellikle bire bir veya tam eşleşme yoktur. Bu nedenle, Azure Ilkesiyle **uyumlu** yalnızca ilke tanımlarının kendilerine başvurur; Bu, bir denetimin tüm gereksinimleriyle tamamen uyumlu olduğunuzdan emin değildir. Buna ek olarak, uyumluluk standardı şu anda herhangi bir Azure Ilke tanımı tarafından açıklanmayan denetimler içerir. Bu nedenle, Azure Ilkesinde uyumluluk, genel uyumluluk durumunuzu yalnızca kısmi görünümüdür. Bu uyumluluk standardı için uyumluluk etki alanları, denetimler ve Azure Ilke tanımları arasındaki ilişkilendirmeler zaman içinde değişebilir. Değişiklik geçmişini görüntülemek için [GitHub kayıt geçmişine](https://github.com/Azure/azure-policy/commits/master/built-in-policies/policySetDefinitions/Regulatory%20Compliance/NIST80053_audit.json)bakın.

## <a name="access-control"></a>Erişim Denetimi

### <a name="access-control-policy-and-procedures"></a>Ilke ve yordamları Access Control

**Kimlik**: nıst SP 800-53 R4 AC-1 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1000-Access Control Ilke ve yordamlar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2ef3cc79-733e-48ed-ab6f-7bf439e9b406) |Microsoft bu Access Control denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1000.json) |
|[Microsoft yönetilen denetim 1001-Access Control Ilke ve yordamlar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4e26f8c3-4bf3-4191-b8fc-d888805101b7) |Microsoft bu Access Control denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1001.json) |

### <a name="account-management"></a>Hesap Yönetimi

**Kimlik**: nıst SP 800-53 R4 AC-2 **sahipliği**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Kullanım dışı bırakılan hesaplar aboneliğinizden kaldırılmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6b1cbf55-e8b6-442f-ba4c-7246b6381474) |Kullanım dışı bırakılan hesaplar aboneliklerinizden kaldırılmalıdır.  Kullanım dışı bırakılan hesaplar, oturum açma işleminden engellenen hesaplardır. |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveDeprecatedAccounts_Audit.json) |
|[Sahip izinleri olan kullanım dışı hesaplar aboneliğinizden kaldırılmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Febb62a0c-3560-49e1-89ed-27e074e9f8ad) |Sahip izinleri olan kullanım dışı hesaplar aboneliğinizden kaldırılmalıdır.  Kullanım dışı bırakılan hesaplar, oturum açma işleminden engellenen hesaplardır. |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveDeprecatedAccountsWithOwnerPermissions_Audit.json) |
|[Sahip izinleri olan dış hesaplar aboneliğinizden kaldırılmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8456c1c-aa66-4dfb-861a-25d127b775c9) |İzlenmeyen erişimi engellemek için sahip izinlerine sahip dış hesapların aboneliğinizden kaldırılması gerekir. |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsWithOwnerPermissions_Audit.json) |
|[Okuma izinlerine sahip dış hesapların aboneliğinizden kaldırılması gerekir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5f76cf89-fbf2-47fd-a3f4-b891fa780b60) |İzlenmeyen erişimi engellemek için, okuma ayrıcalıklarına sahip dış hesapların aboneliğinizden kaldırılması gerekir. |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsReadPermissions_Audit.json) |
|[Yazma izinlerine sahip dış hesapların aboneliğinizden kaldırılması gerekir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5c607a2e-c700-4744-8254-d77e7c9eb5e4) |İzlenmeyen erişimi engellemek için, yazma ayrıcalıklarına sahip dış hesapların aboneliğinizden kaldırılması gerekir. |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsWritePermissions_Audit.json) |
|[Microsoft yönetilen denetim 1002-hesap yönetimi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F632024c2-8079-439d-a7f6-90af1d78cc65) |Microsoft bu Access Control denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1002.json) |
|[Microsoft yönetilen denetim 1003-hesap yönetimi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3b68b179-3704-4ff7-b51d-7d65374d165d) |Microsoft bu Access Control denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1003.json) |
|[Microsoft yönetilen denetim 1004-hesap yönetimi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc17822dc-736f-4eb4-a97d-e6be662ff835) |Microsoft bu Access Control denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1004.json) |
|[Microsoft yönetilen denetim 1005-hesap yönetimi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b626abc-26d4-4e22-9de8-3831818526b1) |Microsoft bu Access Control denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1005.json) |
|[Microsoft yönetilen denetim 1006-hesap yönetimi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faae8d54c-4bce-4c04-b3aa-5b65b67caac8) |Microsoft bu Access Control denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1006.json) |
|[Microsoft yönetilen denetim 1007-hesap yönetimi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17200329-bf6c-46d8-ac6d-abf4641c2add) |Microsoft bu Access Control denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1007.json) |
|[Microsoft yönetilen denetim 1008-hesap yönetimi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8356cfc6-507a-4d20-b818-08038011cd07) |Microsoft bu Access Control denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1008.json) |
|[Microsoft yönetilen denetim 1009-hesap yönetimi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb26f8610-e615-47c2-abd6-c00b2b0b503a) |Microsoft bu Access Control denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1009.json) |
|[Microsoft yönetilen denetim 1010-hesap yönetimi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F784663a8-1eb0-418a-a98c-24d19bc1bb62) |Microsoft bu Access Control denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1010.json) |
|[Microsoft yönetilen denetim 1011-hesap yönetimi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7e6a54f3-883f-43d5-87c4-172dfd64a1f5) |Microsoft bu Access Control denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1011.json) |
|[Microsoft yönetilen denetim 1012-hesap yönetimi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fefd7b9ae-1db6-4eb6-b0fe-87e6565f9738) |Microsoft bu Access Control denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1012.json) |

### <a name="account-management--automated-system-account-management"></a>Hesap yönetimi | Otomatik sistem hesabı yönetimi

**Kimlik**: nıst SP 800-53 R4 AC-2 (1) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1013-hesap yönetimi \| otomatik sistem hesabı yönetimi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8fd7b917-d83b-4379-af60-51e14e316c61) |Microsoft bu Access Control denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1013.json) |

### <a name="account-management--removal-of-temporary--emergency-accounts"></a>Hesap yönetimi | Geçici/acil durum hesaplarının kaldırılması

**Kimlik**: nıst SP 800-53 R4 AC-2 (2) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1014- \| geçici/acil durum hesaplarının hesap yönetimi kaldırması](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5dee936c-8037-4df1-ab35-6635733da48c) |Microsoft bu Access Control denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1014.json) |

### <a name="account-management--disable-inactive-accounts"></a>Hesap yönetimi | Etkin olmayan hesapları devre dışı bırak

**Kimlik**: nıst SP 800-53 R4 AC-2 (3) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1015-hesap yönetimi \| etkin olmayan hesapları devre dışı bırakma](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F544a208a-9c3f-40bc-b1d1-d7e144495c14) |Microsoft bu Access Control denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1015.json) |

### <a name="account-management--automated-audit-actions"></a>Hesap yönetimi | Otomatikleştirilmiş Denetim eylemleri

**Kimlik**: nıst SP 800-53 R4 AC-2 (4) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1016-hesap yönetimi \| Otomatikleştirilmiş Denetim eylemleri](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd8b43277-512e-40c3-ab00-14b3b6e72238) |Microsoft bu Access Control denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1016.json) |

### <a name="account-management--inactivity-logout"></a>Hesap yönetimi | Eylemsizlik dışı oturum kapatma

**Kimlik**: nıst SP 800-53 R4 AC-2 (5) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1017-hesap yönetimi \| eylemsizlik dışı oturum kapatma](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fc3db37-e59a-48c1-84e9-1780cedb409e) |Microsoft bu Access Control denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1017.json) |

### <a name="account-management--role-based-schemes"></a>Hesap yönetimi | Role-Based şemaları

**Kimlik**: nıst SP 800-53 R4 AC-2 (7) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[SQL sunucuları için bir Azure Active Directory Yöneticisi sağlanmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1f314764-cb73-4fc9-b863-8eca98ac36e9) |Azure AD kimlik doğrulamasını etkinleştirmek için SQL sunucunuz için bir Azure Active Directory yöneticisinin sağlamasını denetleyin. Azure AD kimlik doğrulaması, veritabanı kullanıcıları ve diğer Microsoft Hizmetleri için Basitleştirilmiş izin yönetimi ve merkezi kimlik yönetimine izin verebilir |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SQL_DB_AuditServerADAdmins_Audit.json) |
|[Özel RBAC kurallarının kullanımını denetleme](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa451c1ef-c6ca-483d-87ed-f49761e3ffb5) |Hataya açık olan özel RBAC rolleri yerine ' Owner, katılımcısı, Reader ' gibi yerleşik rolleri denetleyin. Özel rolleri kullanmak özel durum olarak değerlendirilir ve kapsamlı bir inceleme ve tehdit modelleme gerektirir |Denetim, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |
|[Microsoft yönetilen denetim 1018-hesap yönetimi \| Role-Based düzenleri](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc9121abf-e698-4ee9-b1cf-71ee528ff07f) |Microsoft bu Access Control denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1018.json) |
|[Microsoft yönetilen denetim 1019-hesap yönetimi \| Role-Based düzenleri](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6a3ee9b2-3977-459c-b8ce-2db583abd9f7) |Microsoft bu Access Control denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1019.json) |
|[Microsoft yönetilen denetim 1020-hesap yönetimi \| Role-Based düzenleri](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b291ee8-3140-4cad-beb7-568c077c78ce) |Microsoft bu Access Control denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1020.json) |
|[Service Fabric kümeler yalnızca istemci kimlik doğrulaması için Azure Active Directory kullanmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb54ed75b-3e1a-44ac-a333-05ba39b99ff0) |İstemci kimlik doğrulamasının kullanımını yalnızca Service Fabric Azure Active Directory aracılığıyla denetle |Denetim, reddetme, devre dışı |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditADAuth_Audit.json) |

### <a name="account-management--restrictions-on-use-of-shared--group-accounts"></a>Hesap yönetimi | Paylaşılan/grup hesaplarının kullanımıyla ilgili kısıtlamalar

**Kimlik**: nıst SP 800-53 R4 AC-2 (9) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1021- \| paylaşılan/grup hesapları kullanımıyla Ilgili Hesap yönetimi kısıtlamaları](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9a3eb0a3-428d-4669-baff-20a14eb4b551) |Microsoft bu Access Control denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1021.json) |

### <a name="account-management--shared--group-account-credential-termination"></a>Hesap yönetimi | Paylaşılan/Grup hesabı kimlik bilgisi sonlandırma

**Kimlik**: nıst SP 800-53 R4 AC-2 (10) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1022-hesap yönetimi \| paylaşılan/grup hesabı kimlik bilgisi sonlandırma](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F411f7e2d-9a0b-4627-a0b9-1700432db47d) |Microsoft bu Access Control denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1022.json) |

### <a name="account-management--usage-conditions"></a>Hesap yönetimi | Kullanım Koşulları

**Kimlik**: nıst SP 800-53 R4 AC-2 (11) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1023-hesap yönetimi \| kullanım koşulları](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe55698b6-3dea-4aa9-99b9-d8218c6ab6e5) |Microsoft bu Access Control denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1023.json) |

### <a name="account-management--account-monitoring--atypical-usage"></a>Hesap yönetimi | Hesap Izleme/genel kullanım

**Kimlik**: nıst SP 800-53 R4 AC-2 (12) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Sanal makinelerin yönetim bağlantı noktaları, tam zamanında ağ erişim denetimiyle korunmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |Olası ağ tam zamanında (JıT) erişim, Azure Güvenlik Merkezi tarafından öneriler olarak izlenir |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |
|[Microsoft yönetilen denetim 1024-hesap yönetimi \| hesabı izleme/genel kullanım](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F84914fb4-12da-4c53-a341-a9fd463bed10) |Microsoft bu Access Control denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1024.json) |
|[Microsoft yönetilen denetim 1025-hesap yönetimi \| hesabı izleme/genel kullanım](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fadfe020d-0a97-45f4-a39c-696ef99f3a95) |Microsoft bu Access Control denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1025.json) |

### <a name="account-management--disable-accounts-for-high-risk-individuals"></a>Hesap yönetimi | High-Risk bireyler için hesapları devre dışı bırakma

**Kimlik**: nıst SP 800-53 R4 AC-2 (13) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1026-hesap yönetimi \| High-Risk kişiler Için hesapları devre dışı bırakma](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F55419419-c597-4cd4-b51e-009fd2266783) |Microsoft bu Access Control denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1026.json) |

### <a name="access-enforcement"></a>Erişim zorlaması

**Kimlik**: nıst SP 800-53 R4 AC-3 **sahipliği**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1027-erişim zorlaması](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa76ca9b0-3f4a-4192-9a38-b25e4f8ae48c) |Microsoft bu Access Control denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1027.json) |

### <a name="information-flow-enforcement"></a>Bilgi akışı zorlaması

**Kimlik**: nıst SP 800-53 R4 AC-4 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[CORS, her kaynağın Web Uygulamalarınıza erişmesine izin vermemelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5744710e-cc2f-4ee8-8809-3b11e89f4bc9) |Çıkış noktaları arası kaynak paylaşımı (CORS), tüm etki alanlarının Web uygulamanıza erişmesine izin vermemelidir. Yalnızca gerekli etki alanlarının Web uygulamanızla etkileşime girmesine izin verin. |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RestrictCORSAccess_WebApp_Audit.json) |
|[Microsoft yönetilen denetim 1028-bilgi akışı zorlaması](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff171df5c-921b-41e9-b12b-50801c315475) |Microsoft bu Access Control denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1028.json) |

### <a name="information-flow-enforcement--security-policy-filters"></a>Bilgi akışı zorlaması | Güvenlik Ilkesi filtreleri

**Kimlik**: nıst SP 800-53 R4 AC-4 (8) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1029-bilgi akışı zorlaması \| Güvenlik Ilkesi filtreleri](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F53ac8f8e-c2b5-4d44-8a2d-058e9ced9b69) |Microsoft bu Access Control denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1029.json) |

### <a name="information-flow-enforcement--physical--logical-separation-of-information-flows"></a>Bilgi akışı zorlaması | Bilgi akışlarının fiziksel/mantıksal ayrımı

**Kimlik**: nıst SP 800-53 R4 AC-4 (21) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft tarafından yönetilen denetim 1030-bilgi akışı zorlaması \| fiziksel/bilgi akışları 'Nın mantıksal ayrımı](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd3531453-b869-4606-9122-29c1cd6e7ed1) |Microsoft bu Access Control denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1030.json) |

### <a name="separation-of-duties"></a>Görevlerin ayrımı

**Kimlik**: nıst SP 800-53 R4 AC-5 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Aboneliğiniz için en fazla 3 sahip belirtilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f11b553-d42e-4e3a-89be-32ca364cad4c) |Güvenliği aşılmış bir sahibe göre ihlal olasılığını azaltmak için 3 adede kadar abonelik sahibi belirlemeniz önerilir. |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateLessThanXOwners_Audit.json) |
|[Yöneticiler grubunda belirtilen üyelerin hiçbiri eksik olan Windows makinelerini denetle](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F30f71ea1-ac77-4f26-9fc5-2d926bbd4ba7) |Önkoşulların ilke atama kapsamına dağıtılmasını gerektirir. Ayrıntılar için, adresini ziyaret edin [https://aka.ms/gcpol](https://aka.ms/gcpol) . Yerel Yöneticiler grubu, ilke parametresinde listelenen bir veya daha fazla üye içermiyorsa makineler uyumlu değildir. |Auditınotexists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToInclude_AINE.json) |
|[Yöneticiler grubunda belirtilen üyelere sahip olan Windows makinelerini denetle](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F69bf4abd-ca1e-4cf6-8b5a-762d42e61d4f) |Önkoşulların ilke atama kapsamına dağıtılmasını gerektirir. Ayrıntılar için, adresini ziyaret edin [https://aka.ms/gcpol](https://aka.ms/gcpol) . Yerel Yöneticiler grubu, ilke parametresinde listelenen bir veya daha fazla üyeyi içeriyorsa makineler uyumlu değildir. |Auditınotexists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToExclude_AINE.json) |
|[Microsoft yönetilen denetim 1031-görevlerin ayrımı](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6b93a801-fe25-4574-a60d-cb22acffae00) |Microsoft bu Access Control denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1031.json) |
|[Microsoft yönetilen denetim 1032-görevlerin ayrımı](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5aa85661-d618-46b8-a20f-ca40a86f0751) |Microsoft bu Access Control denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1032.json) |
|[Microsoft yönetilen denetim 1033-görevlerin ayrımı](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F48540f01-fc11-411a-b160-42807c68896e) |Microsoft bu Access Control denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1033.json) |
|[Aboneliğinize birden fazla sahip atanmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09024ccc-0c5f-475e-9457-b7c0d9ed487b) |Yönetici erişiminin artıklığını sağlamak için birden fazla abonelik sahibi atamanız önerilir. |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateMoreThanOneOwner_Audit.json) |

### <a name="least-privilege"></a>En az ayrıcalık

**Kimlik**: nıst SP 800-53 R4 AC-6 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1034-en az ayrıcalık](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F02a5ed00-6d2e-4e97-9a98-46c32c057329) |Microsoft bu Access Control denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1034.json) |

### <a name="least-privilege--authorize-access-to-security-functions"></a>En az ayrıcalık | Güvenlik Işlevlerine erişim yetkisi verme

**Kimlik**: nıst SP 800-53 R4 AC-6 (1) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1035-en az ayrıcalık \| güvenlik Işlevlerine erişim yetkisi verir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fca94b046-45e2-444f-a862-dc8ce262a516) |Microsoft bu Access Control denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1035.json) |

### <a name="least-privilege--non-privileged-access-for-nonsecurity-functions"></a>En az ayrıcalık | Güvenlikle Ilgili olmayan Işlevler için ayrıcalıksız erişim

**Kimlik**: nıst SP 800-53 R4 AC-6 (2) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1036- \| güvenlikle ilgili olmayan Işlevler Için ayrıcalıklı ayrıcalıksız erişim](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9a16d673-8cf0-4dcf-b1d5-9b3e114fef71) |Microsoft bu Access Control denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1036.json) |

### <a name="least-privilege--network-access-to-privileged-commands"></a>En az ayrıcalık | Ayrıcalıklı komutlara ağ erişimi

**Kimlik**: nıst SP 800-53 R4 AC-6 (3) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1037-ayrıcalıklı komutlara en az ayrıcalık \| ağ erişimi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffa4c2a3d-1294-41a3-9ada-0e540471e9fb) |Microsoft bu Access Control denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1037.json) |

### <a name="least-privilege--privileged-accounts"></a>En az ayrıcalık | Ayrıcalıklı hesaplar

**Kimlik**: nıst SP 800-53 R4 AC-6 (5) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1038-en az ayrıcalık \| ayrıcalıklı hesaplar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F26692e88-71b7-4a5f-a8ac-9f31dd05bd8e) |Microsoft bu Access Control denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1038.json) |

### <a name="least-privilege--review-of-user-privileges"></a>En az ayrıcalık | Kullanıcı Ayrıcalıklarını Gözden geçirme

**Kimlik**: nıst SP 800-53 R4 AC-6 (7) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Aboneliğiniz için en fazla 3 sahip belirtilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f11b553-d42e-4e3a-89be-32ca364cad4c) |Güvenliği aşılmış bir sahibe göre ihlal olasılığını azaltmak için 3 adede kadar abonelik sahibi belirlemeniz önerilir. |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateLessThanXOwners_Audit.json) |
|[Yöneticiler grubunda belirtilen üyelerin hiçbiri eksik olan Windows makinelerini denetle](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F30f71ea1-ac77-4f26-9fc5-2d926bbd4ba7) |Önkoşulların ilke atama kapsamına dağıtılmasını gerektirir. Ayrıntılar için, adresini ziyaret edin [https://aka.ms/gcpol](https://aka.ms/gcpol) . Yerel Yöneticiler grubu, ilke parametresinde listelenen bir veya daha fazla üye içermiyorsa makineler uyumlu değildir. |Auditınotexists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToInclude_AINE.json) |
|[Yöneticiler grubunda belirtilen üyelere sahip olan Windows makinelerini denetle](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F69bf4abd-ca1e-4cf6-8b5a-762d42e61d4f) |Önkoşulların ilke atama kapsamına dağıtılmasını gerektirir. Ayrıntılar için, adresini ziyaret edin [https://aka.ms/gcpol](https://aka.ms/gcpol) . Yerel Yöneticiler grubu, ilke parametresinde listelenen bir veya daha fazla üyeyi içeriyorsa makineler uyumlu değildir. |Auditınotexists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToExclude_AINE.json) |
|[Microsoft yönetilen denetim 1039-kullanıcı ayrıcalıklarının en az ayrıcalık \| incelemesi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3a7b9de4-a8a2-4672-914d-c5f6752aa7f9) |Microsoft bu Access Control denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1039.json) |
|[Microsoft yönetilen denetim 1040-kullanıcı ayrıcalıklarının en az ayrıcalık \| incelemesi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F54205576-cec9-463f-ba44-b4b3f5d0a84c) |Microsoft bu Access Control denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1040.json) |
|[Aboneliğinize birden fazla sahip atanmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09024ccc-0c5f-475e-9457-b7c0d9ed487b) |Yönetici erişiminin artıklığını sağlamak için birden fazla abonelik sahibi atamanız önerilir. |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateMoreThanOneOwner_Audit.json) |

### <a name="least-privilege--privilege-levels-for-code-execution"></a>En az ayrıcalık | Kod yürütme için ayrıcalık düzeyleri

**Kimlik**: nıst SP 800-53 R4 AC-6 (8) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1041- \| kod yürütme Için en az ayrıcalık ayrıcalık düzeyleri](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb3d8d15b-627a-4219-8c96-4d16f788888b) |Microsoft bu Access Control denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1041.json) |

### <a name="least-privilege--auditing-use-of-privileged-functions"></a>En az ayrıcalık | Ayrıcalıklı Işlevlerin kullanımını denetleme

**Kimlik**: nıst SP 800-53 R4 AC-6 (9) **sahipliği**: Microsoft

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1042-ayrıcalıklı Işlevlerin en az ayrıcalık \| denetimini kullanma](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F319dc4f0-0fed-4ac9-8fc3-7aeddee82c07) |Microsoft bu Access Control denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1042.json) |

### <a name="least-privilege--prohibit-non-privileged-users-from-executing-privileged-functions"></a>En az ayrıcalık | Ayrıcalıklı olmayan kullanıcıların ayrıcalıklı Işlevler yürütmesini yasakla

**Kimlik**: nıst SP 800-53 R4 AC-6 (10) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1043-en az ayrıcalık, \| ayrıcalıklı olmayan kullanıcıların ayrıcalıklı Işlevler yürütmesini yasaklıyor](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F361a77f6-0f9c-4748-8eec-bc13aaaa2455) |Microsoft bu Access Control denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1043.json) |

### <a name="unsuccessful-logon-attempts"></a>Başarısız oturum açma girişimleri

**Kimlik**: nıst SP 800-53 R4 AC-7 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1044-başarısız oturum açma girişimleri](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0abbac52-57cf-450d-8408-1208d0dd9e90) |Microsoft bu Access Control denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1044.json) |
|[Microsoft yönetilen denetim 1045-başarısız oturum açma girişimleri](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F554d2dd6-f3a8-4ad5-b66f-5ce23bd18892) |Microsoft bu Access Control denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1045.json) |

### <a name="unsuccessful-logon-attempts--purge--wipe-mobile-device"></a>Başarısız oturum açma girişimleri | Mobil cihazı Temizleme/silme

**Kimlik**: nıst SP 800-53 R4 AC-7 (2) **sahiplik**: Microsoft

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1046-otomatik hesap kilit \| Temizleme/silme mobil cihaz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b1aa965-7502-41f9-92be-3e2fe7cc392a) |Microsoft bu Access Control denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1046.json) |

### <a name="system-use-notification"></a>Sistem kullanım bildirimi

**Kimlik**: nıst SP 800-53 R4 AC-8 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1047-sistem kullanım bildirimi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1ff6d62-a55c-41ab-90ba-90bb5b7b6f62) |Microsoft bu Access Control denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1047.json) |
|[Microsoft yönetilen denetim 1048-sistem kullanım bildirimi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F483e7ca9-82b3-45a2-be97-b93163a0deb7) |Microsoft bu Access Control denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1048.json) |
|[Microsoft yönetilen denetim 1049-Sistem kullanım bildirimi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9adf7ba7-900a-4f35-8d57-9f34aafc405c) |Microsoft bu Access Control denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1049.json) |

### <a name="concurrent-session-control"></a>Eşzamanlı oturum denetimi

**Kimlik**: nıst SP 800-53 R4 AC-10 **sahipliği**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1050-eşzamanlı oturum denetimi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbd20184c-b4ec-4ce5-8db6-6e86352d183f) |Microsoft bu Access Control denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1050.json) |

### <a name="session-lock"></a>Oturum kilidi

**Kimlik**: nıst SP 800-53 R4 AC-11 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1051-oturum kilidi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7cac6ee9-b58b-40c8-a5ce-f0efc3d9b339) |Microsoft bu Access Control denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1051.json) |
|[Microsoft yönetilen denetim 1052-oturum kilidi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F027cae1c-ec3e-4492-9036-4168d540c42a) |Microsoft bu Access Control denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1052.json) |

### <a name="session-lock--pattern-hiding-displays"></a>Oturum kilidi | Pattern-Hiding görüntüler

**Kimlik**: nıst SP 800-53 R4 AC-11 (1) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1053-oturum kilidi \| Pattern-Hiding görüntüler](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7582b19c-9dba-438e-aed8-ede59ac35ba3) |Microsoft bu Access Control denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1053.json) |

### <a name="session-termination"></a>Oturum sonlandırma

**Kimlik**: nıst SP 800-53 R4 AC-12 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1054-oturum sonlandırma](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5807e1b4-ba5e-4718-8689-a0ca05a191b2) |Microsoft bu Access Control denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1054.json) |

### <a name="session-termination--user-initiated-logouts--message-displays"></a>Oturum sonlandırma | User-Initiated günlüğe kaydetme/Ileti görüntüleme

**Kimlik**: nıst SP 800-53 R4 AC-12 (1) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1055-oturum sonlandırma \| User-Initiated günlüğe kaydetme/Ileti görüntüleme](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F769efd9b-3587-4e22-90ce-65ddcd5bd969) |Microsoft bu Access Control denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1055.json) |
|[Microsoft yönetilen denetim 1056-oturum sonlandırma \| User-Initiated günlüğe kaydetme/Ileti görüntüleme](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac43352f-df83-4694-8738-cfce549fd08d) |Microsoft bu Access Control denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1056.json) |

### <a name="permitted-actions-without-identification-or-authentication"></a>Tanımlayıcı veya kimlik doğrulaması olmayan izin verilen eylemler

**Kimlik**: nıst SP 800-53 R4 AC-14 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1057-tanımlama veya kimlik doğrulaması olmadan Izin verilen eylemler](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F78255758-6d45-4bf0-a005-7016bc03b13c) |Microsoft bu Access Control denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1057.json) |
|[Microsoft yönetilen denetim 1058-tanımlama veya kimlik doğrulaması olmadan Izin verilen eylemler](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F76e85d08-8fbb-4112-a1c1-93521e6a9254) |Microsoft bu Access Control denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1058.json) |

### <a name="security-attributes"></a>Güvenlik Öznitelikleri

**Kimlik**: nıst SP 800-53 R4 AC-16 **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Gelişmiş veri güvenliği SQL yönetilen örneği üzerinde etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |Gelişmiş veri güvenliği olmadan her SQL yönetilen örneğini denetleyin. |Auditınotexists, devre dışı |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[Gelişmiş veri güvenliği SQL sunucularınızda etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |Gelişmiş veri güvenliği olmadan SQL sunucularını denetleme |Auditınotexists, devre dışı |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |

### <a name="remote-access"></a>Uzaktan Erişim

**Kimlik**: nıst SP 800-53 R4 AC-17 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1059-uzaktan erişim](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa29b5d9f-4953-4afe-b560-203a6410b6b4) |Microsoft bu Access Control denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1059.json) |
|[Microsoft yönetilen denetim 1060-uzaktan erişim](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34a987fd-2003-45de-a120-014956581f2b) |Microsoft bu Access Control denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1060.json) |

### <a name="remote-access--automated-monitoring--control"></a>Uzaktan erişim | Otomatik Izleme/denetim

**Kimlik**: nıst SP 800-53 R4 AC-17 (1) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Kimliği olmayan sanal makinelerde Konuk yapılandırma atamalarını etkinleştirmek için sistem tarafından atanan yönetilen kimlik Ekle](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3cf2ab00-13f1-4d0c-8971-2ac904541a7e) |Bu ilke, Azure 'da barındırılan, Konuk yapılandırması tarafından desteklenen ancak herhangi bir yönetilen kimliği bulunmayan sanal makinelere sistem tarafından atanan yönetilen bir kimlik ekler. Sistem tarafından atanan yönetilen kimlik, tüm konuk yapılandırma atamaları için bir önkoşuldur ve herhangi bir konuk yapılandırma ilkesi tanımı kullanılmadan önce makinelere eklenmelidir. Konuk yapılandırması hakkında daha fazla bilgi için, adresini ziyaret edin [https://aka.ms/gcpol](https://aka.ms/gcpol) . |değiştir |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AddSystemIdentityWhenNone_Prerequisite.json) |
|[Kullanıcı tarafından atanan bir kimliğe sahip VM 'lerde Konuk yapılandırma atamalarını etkinleştirmek için sistem tarafından atanan yönetilen kimlik Ekle](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F497dff13-db2a-4c0f-8603-28fa3b331ab6) |Bu ilke, Azure 'da barındırılan sanal makinelere, Konuk yapılandırması tarafından desteklenen ve en az bir kullanıcı tarafından atanan kimliğe sahip ancak sistem tarafından atanan bir yönetilen kimliğe sahip olmayan bir yönetilen kimlik ekler. Sistem tarafından atanan yönetilen kimlik, tüm konuk yapılandırma atamaları için bir önkoşuldur ve herhangi bir konuk yapılandırma ilkesi tanımı kullanılmadan önce makinelere eklenmelidir. Konuk yapılandırması hakkında daha fazla bilgi için, adresini ziyaret edin [https://aka.ms/gcpol](https://aka.ms/gcpol) . |değiştir |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AddSystemIdentityWhenUser_Prerequisite.json) |
|[Parolasız hesaplardan uzak bağlantılara izin veren Linux makinelerini denetleme](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea53dbee-c6c9-4f0e-9f9e-de0039b78023) |Önkoşulların ilke atama kapsamına dağıtılmasını gerektirir. Ayrıntılar için, adresini ziyaret edin [https://aka.ms/gcpol](https://aka.ms/gcpol) . Linux makineleri parolasız olan hesaplardan uzak bağlantılara izin veren makineler uyumlu değil |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_LinuxPassword110_AINE.json) |
|[Linux VM 'lerinde Konuk yapılandırma atamalarını etkinleştirmek için Linux konuk yapılandırma uzantısını dağıtma](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F331e8ea8-378a-410f-a2e5-ae22f38bb0da) |Bu ilke, Linux konuk yapılandırma uzantısını Azure 'da barındırılan ve konuk yapılandırması tarafından desteklenen Linux sanal makinelerine dağıtır. Linux konuk yapılandırma uzantısı, tüm Linux konuk yapılandırma atamaları için bir önkoşuldur ve Linux konuk yapılandırma ilkesi tanımı kullanılmadan önce makinelere dağıtılmalıdır. Konuk yapılandırması hakkında daha fazla bilgi için, adresini ziyaret edin [https://aka.ms/gcpol](https://aka.ms/gcpol) . |deployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_DeployExtensionLinux_Prerequisite.json) |
|[Windows VM 'lerde Konuk yapılandırma atamalarını etkinleştirmek için Windows Konuk yapılandırma uzantısı 'nı dağıtma](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F385f5831-96d4-41db-9a3c-cd3af78aaae6) |Bu ilke, Windows Konuk yapılandırma uzantısını Azure 'da barındırılan ve konuk yapılandırması tarafından desteklenen Windows sanal makinelerine dağıtır. Windows Konuk yapılandırma uzantısı, tüm Windows Konuk yapılandırma atamaları için bir önkoşuldur ve herhangi bir Windows Konuk yapılandırma ilkesi tanımı kullanılmadan önce makinelere dağıtılmalıdır. Konuk yapılandırması hakkında daha fazla bilgi için, adresini ziyaret edin [https://aka.ms/gcpol](https://aka.ms/gcpol) . |deployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_DeployExtensionWindows_Prerequisite.json) |
|[Microsoft yönetilen denetim 1061-uzaktan erişim \| otomatik izleme/denetim](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7ac22808-a2e8-41c4-9d46-429b50738914) |Microsoft bu Access Control denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1061.json) |
|[API Apps için uzaktan hata ayıklama kapatılmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe9c8d085-d9cc-4b17-9cdc-059f1f01f19e) |Uzaktan hata ayıklama, API uygulamalarında gelen bağlantı noktalarının açılmasını gerektirir. Uzaktan hata ayıklama kapalı olmalıdır. |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_ApiApp_Audit.json) |
|[Işlev uygulamaları için uzaktan hata ayıklama kapatılmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e60b895-3786-45da-8377-9c6b4b6ac5f9) |Uzaktan hata ayıklama, işlev uygulamalarında gelen bağlantı noktalarının açılmasını gerektirir. Uzaktan hata ayıklama kapalı olmalıdır. |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_FunctionApp_Audit.json) |
|[Web uygulamaları için uzaktan hata ayıklama kapatılmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcb510bfd-1cba-4d9f-a230-cb0976f4bb71) |Uzaktan hata ayıklama, gelen bağlantı noktalarının bir Web uygulamasında açılmasını gerektirir. Uzaktan hata ayıklama kapalı olmalıdır. |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_WebApp_Audit.json) |
|[Depolama hesapları, ağ erişimini kısıtlıyor olmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34c877ad-507e-4c82-993e-3452a6e0ad3c) |Depolama hesaplarına ağ erişimi kısıtlanmalıdır. Ağ kurallarını, yalnızca izin verilen ağların uygulamalarının depolama hesabına erişebilmesi için yapılandırın. Belirli internet veya şirket içi istemcilerden gelen bağlantılara izin vermek için, belirli Azure sanal ağlarından veya genel İnternet IP adresi aralıklarına yönelik trafiğe erişim verilebilir |Denetim, reddetme, devre dışı |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |

### <a name="remote-access--protection-of-confidentiality--integrity-using-encryption"></a>Uzaktan erişim | Şifreleme kullanılarak gizlilik/bütünlük koruması

**Kimlik**: nıst SP 800-53 R4 AC-17 (2) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1062- \| şifreleme kullanarak gizliliği/bütünlüğü uzaktan erişim koruması](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4708723f-e099-4af1-bbf9-b6df7642e444) |Microsoft bu Access Control denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1062.json) |

### <a name="remote-access--managed-access-control-points"></a>Uzaktan erişim | Yönetilen Access Control noktaları

**Kimlik**: nıst SP 800-53 R4 AC-17 (3) **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1063-uzaktan erişim \| yönetilen Access Control noktaları](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F593ce201-54b2-4dd0-b34f-c308005d7780) |Microsoft bu Access Control denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1063.json) |

### <a name="remote-access--privileged-commands--access"></a>Uzaktan erişim | Ayrıcalıklı komutlar/erişim

**Kimlik**: nıst SP 800-53 R4 AC-17 (4) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1064-uzaktan erişim \| ayrıcalıklı komutlar/erişim](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feb4d9508-cbf0-4a3c-bb5c-6c95b159f3fb) |Microsoft bu Access Control denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1064.json) |
|[Microsoft yönetilen denetim 1065-uzaktan erişim \| ayrıcalıklı komutlar/erişim](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff87b8085-dca9-4cf1-8f7b-9822b997797c) |Microsoft bu Access Control denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1065.json) |

### <a name="remote-access--disconnect--disable-access"></a>Uzaktan erişim | Bağlantıyı kes/erişimi devre dışı bırak

**Kimlik**: nıst SP 800-53 R4 AC-17 (9) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1066-uzaktan erişim \| bağlantıyı kesme/erişimi devre dışı bırakma](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4455c2e8-c65d-4acf-895e-304916f90b36) |Microsoft bu Access Control denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1066.json) |

### <a name="wireless-access"></a>Kablosuz erişim

**Kimlik**: nıst SP 800-53 R4 AC-18 **sahiplik**: Microsoft

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1067-kablosuz erişim](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5c5e54f6-0127-44d0-8b61-f31dc8dd6190) |Microsoft bu Access Control denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1067.json) |
|[Microsoft yönetilen denetim 1068-kablosuz erişim](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2d045bca-a0fd-452e-9f41-4ec33769717c) |Microsoft bu Access Control denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1068.json) |

### <a name="wireless-access--authentication-and-encryption"></a>Kablosuz erişim | Kimlik doğrulama ve şifreleme

**Kimlik**: nıst SP 800-53 R4 AC-18 (1) **sahiplik**: Microsoft

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1069-kablosuz erişim \| kimlik doğrulaması ve şifreleme](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F91c97b44-791e-46e9-bad7-ab7c4949edbb) |Microsoft bu Access Control denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1069.json) |

### <a name="wireless-access--disable-wireless-networking"></a>Kablosuz erişim | Kablosuz ağı devre dışı bırak

**Kimlik**: nıst SP 800-53 R4 AC-18 (3) **sahiplik**: Microsoft

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1070-kablosuz erişim \| Kablosuz ağı devre dışı bırak](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F68f837d0-8942-4b1e-9b31-be78b247bda8) |Microsoft bu Access Control denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1070.json) |

### <a name="wireless-access--restrict-configurations-by-users"></a>Kablosuz erişim | Konfigürasyonları kullanıcılara göre kısıtla

**Kimlik**: nıst SP 800-53 R4 AC-18 (4) **sahiplik**: Microsoft

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1071-kablosuz erişim, \| kullanıcılara göre konfigürasyonları kısıtlar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1a437f5b-9ad6-4f28-8861-de404d511ae4) |Microsoft bu Access Control denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1071.json) |

### <a name="wireless-access--antennas--transmission-power-levels"></a>Kablosuz erişim | Antenler/Iletim güç düzeyleri

**Kimlik**: nıst SP 800-53 R4 AC-18 (5) **sahiplik**: Microsoft

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1072-kablosuz erişim \| antenler/Iletim güç düzeyleri](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1ca29e41-34ec-4e70-aba9-6248aca18c31) |Microsoft bu Access Control denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1072.json) |

### <a name="access-control-for-mobile-devices"></a>Mobil cihazlar için Access Control

**Kimlik**: nıst SP 800-53 R4 AC-19 **sahiplik**: Microsoft

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1073-mobil cihazlar Için Access Control](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fab55cdb0-c7dd-4bd8-ae22-a7cea7594e9c) |Microsoft bu Access Control denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1073.json) |
|[Microsoft yönetilen denetim 1074-mobil cihazlar Için Access Control](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F27a69937-af92-4198-9b86-08d355c7e59a) |Microsoft bu Access Control denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1074.json) |

### <a name="access-control-for-mobile-devices--full-device--container-based--encryption"></a>Mobil cihazlar için Access Control | Tam cihaz/Container-Based şifreleme

**Kimlik**: nıst SP 800-53 R4 AC-19 (5) **sahiplik**: Microsoft

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1075-mobil cihazlar Için Access Control \| tam cihaz/Container-Based şifreleme](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffc933d22-04df-48ed-8f87-22a3773d4309) |Microsoft bu Access Control denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1075.json) |

### <a name="use-of-external-information-systems"></a>Dış bilgi sistemlerinin kullanımı

**Kimlik**: nıst SP 800-53 R4 AC-20 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1076-dış bilgi sistemlerinin kullanımı](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F98a4bd5f-6436-46d4-ad00-930b5b1dfed4) |Microsoft bu Access Control denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1076.json) |
|[Microsoft yönetilen denetim 1077-dış bilgi sistemlerinin kullanımı](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2dad3668-797a-412e-a798-07d3849a7a79) |Microsoft bu Access Control denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1077.json) |

### <a name="use-of-external-information-systems--limits-on-authorized-use"></a>Dış bilgi sistemlerinin kullanımı | Yetkili kullanım sınırları

**Kimlik**: nıst SP 800-53 R4 AC-20 (1) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1078-dış bilgi sistemleri \| sınırlarını yetkilendirme kullanımı](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb25faf85-8a16-4f28-8e15-d05c0072d64d) |Microsoft bu Access Control denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1078.json) |
|[Microsoft yönetilen denetim 1079-dış bilgi sistemleri \| sınırlarını yetkilendirme kullanımı](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F85c32733-7d23-4948-88da-058e2c56b60f) |Microsoft bu Access Control denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1079.json) |

### <a name="use-of-external-information-systems--portable-storage-devices"></a>Dış bilgi sistemlerinin kullanımı | Taşınabilir depolama cihazları

**Kimlik**: nıst SP 800-53 R4 AC-20 (2) **sahiplik**: Microsoft

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1080-dış bilgi sistemleri \| taşınabilir depolama cihazlarını kullanma](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F852981b4-a380-4704-aa1e-2e52d63445e5) |Microsoft bu Access Control denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1080.json) |

### <a name="information-sharing"></a>Bilgi paylaşımı

**Kimlik**: nıst SP 800-53 R4 AC-21 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1081-bilgi paylaşımı](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3867f2a9-23bb-4729-851f-c3ad98580caf) |Microsoft bu Access Control denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1081.json) |
|[Microsoft yönetilen denetim 1082-bilgi paylaşımı](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F24d480ef-11a0-4b1b-8e70-4e023bf2be23) |Microsoft bu Access Control denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1082.json) |

### <a name="publicly-accessible-content"></a>Herkese açık olan Içerik

**Kimlik**: nıst SP 800-53 R4 AC-22 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1083-genel olarak erişilebilen Içerik](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4e319cb6-2ca3-4a58-ad75-e67f484e50ec) |Microsoft bu Access Control denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1083.json) |
|[Microsoft yönetilen denetim 1084-genel olarak erişilebilen Içerik](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd0eb15db-dd1c-4d1d-b200-b12dd6cd060c) |Microsoft bu Access Control denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1084.json) |
|[Microsoft yönetilen denetim 1085-genel olarak erişilebilen Içerik](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F13d117e0-38b0-4bbb-aaab-563be5dd10ba) |Microsoft bu Access Control denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1085.json) |
|[Microsoft yönetilen denetim 1086-genel olarak erişilebilen Içerik](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffb321e6f-16a0-4be3-878f-500956e309c5) |Microsoft bu Access Control denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1086.json) |

## <a name="awareness-and-training"></a>Tanıma ve eğitim

### <a name="security-awareness-and-training-policy-and-procedures"></a>Güvenlik tanıma ve eğitim Ilkesi ve yordamları

**Kimlik**: nıst SP 800-53 R4-1 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1087-güvenlik tanıma ve eğitim Ilkesi ve yordamları](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F100c82ba-42e9-4d44-a2ba-94b209248583) |Microsoft bu tanıma ve eğitim denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1087.json) |
|[Microsoft yönetilen denetim 1088-güvenlik tanıma ve eğitim Ilkesi ve yordamları](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1d50f99d-1356-49c0-934a-45f742ba7783) |Microsoft bu tanıma ve eğitim denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1088.json) |

### <a name="security-awareness-training"></a>Güvenlik tanıma Eğitimi

**Kimlik**: nıst SP 800-53 R4-2 **sahipliği**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1089-güvenlik tanıma Eğitimi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef080e67-0d1a-4f76-a0c5-fb9b0358485e) |Microsoft bu tanıma ve eğitim denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1089.json) |
|[Microsoft yönetilen denetim 1090-güvenlik tanıma Eğitimi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2fb740e5-cbc7-4d10-8686-d1bf826652b1) |Microsoft bu tanıma ve eğitim denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1090.json) |
|[Microsoft yönetilen denetim 1091-güvenlik tanıma Eğitimi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb23bd715-5d1c-4e5c-9759-9cbdf79ded9d) |Microsoft bu tanıma ve eğitim denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1091.json) |

### <a name="security-awareness-training--insider-threat"></a>Güvenlik tanıma Eğitimi | Insider tehdidi

**Kimlik**: nıst SP 800-53 R4 at-2 (2) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1092-güvenlik tanıma Eğitimi \| Insider tehdidi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8a29d47b-8604-4667-84ef-90d203fcb305) |Microsoft bu tanıma ve eğitim denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1092.json) |

### <a name="role-based-security-training"></a>Role-Based güvenlik eğitimi

**Kimlik**: nıst SP 800-53 R4-3 **sahipliği**: Shared

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1093-Role-Based güvenlik eğitimi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7a0bdeeb-15f4-47e8-a1da-9f769f845fdf) |Microsoft bu tanıma ve eğitim denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1093.json) |
|[Microsoft yönetilen denetim 1094-Role-Based güvenlik eğitimi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4b1853e0-8973-446b-b567-09d901d31a09) |Microsoft bu tanıma ve eğitim denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1094.json) |
|[Microsoft yönetilen denetim 1095-Role-Based güvenlik eğitimi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbc3f6f7a-057b-433e-9834-e8c97b0194f6) |Microsoft bu tanıma ve eğitim denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1095.json) |

### <a name="role-based-security-training--practical-exercises"></a>Role-Based güvenlik eğitimi | Pratik alýþtýrmalar

**Kimlik**: nıst SP 800-53 R4-3 (3) **sahipliği**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1096-Role-Based güvenlik eğitimi \| pratik alıştırmaları](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F420c1477-aa43-49d0-bd7e-c4abdd9addff) |Microsoft bu tanıma ve eğitim denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1096.json) |

### <a name="role-based-security-training--suspicious-communications-and-anomalous-system-behavior"></a>Role-Based güvenlik eğitimi | Şüpheli Iletişimler ve anormal sistem davranışı

**Kimlik**: nıst SP 800-53 R4 at-3 (4) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1097-Role-Based güvenlik eğitimi \| şüpheli Iletişim ve anormal sistem davranışı](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf3e4836-f19e-47eb-a8cd-c3ca150452c0) |Microsoft bu tanıma ve eğitim denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1097.json) |

### <a name="security-training-records"></a>Güvenlik eğitimi kayıtları

**Kimlik**: nıst SP 800-53 R4 at-4 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1098-güvenlik eğitimi kayıtları](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F84363adb-dde3-411a-9fc1-36b56737f822) |Microsoft bu tanıma ve eğitim denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1098.json) |
|[Microsoft yönetilen denetim 1099-güvenlik eğitimi kayıtları](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F01910bab-8639-4bd0-84ef-cc53b24d79ba) |Microsoft bu tanıma ve eğitim denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1099.json) |

## <a name="audit-and-accountability"></a>Denetim ve sorumluluk

### <a name="audit-and-accountability-policy-and-procedures"></a>Denetim ve sorumluluk Ilkesi ve yordamları

**Kimlik**: nıst SP 800-53 R4 au-1 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1100-denetim ve sorumluluk Ilkesi ve yordamları](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4057863c-ca7d-47eb-b1e0-503580cba8a4) |Microsoft bu denetim ve sorumluluk denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1100.json) |
|[Microsoft yönetilen denetim 1101-denetim ve sorumluluk Ilkesi ve yordamları](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7327b708-f0e0-457d-9d2a-527fcc9c9a65) |Microsoft bu denetim ve sorumluluk denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1101.json) |

### <a name="audit-events"></a>Denetim Olayları

**Kimlik**: nıst SP 800-53 R4 au-2 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1102-denetim olayları](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9943c16a-c54c-4b4a-ad28-bfd938cdbf57) |Microsoft bu denetim ve sorumluluk denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1102.json) |
|[Microsoft yönetilen denetim 1103-denetim olayları](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F16feeb31-6377-437e-bbab-d7f73911896d) |Microsoft bu denetim ve sorumluluk denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1103.json) |
|[Microsoft yönetilen denetim 1104-denetim olayları](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcdd8d244-18b2-4306-a1d1-df175ae0935f) |Microsoft bu denetim ve sorumluluk denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1104.json) |
|[Microsoft yönetilen denetim 1105-denetim olayları](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b73f57b-587d-4470-a344-0b0ae805f459) |Microsoft bu denetim ve sorumluluk denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1105.json) |

### <a name="audit-events--reviews-and-updates"></a>Denetim olayları | İncelemeler ve güncelleştirmeler

**Kimlik**: nıst SP 800-53 R4 au-2 (3) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1106-denetim olayları \| Incelemeleri ve güncelleştirmeleri](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd2b4feae-61ab-423f-a4c5-0e38ac4464d8) |Microsoft bu denetim ve sorumluluk denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1106.json) |

### <a name="content-of-audit-records"></a>Denetim kayıtlarının içeriği

**Kimlik**: nıst SP 800-53 R4 au-3 **sahipliği**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1107-denetim kayıtlarının Içeriği](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb29ed931-8e21-4779-8458-27916122a904) |Microsoft bu denetim ve sorumluluk denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1107.json) |

### <a name="content-of-audit-records--additional-audit-information"></a>Denetim kayıtlarının içeriği | Ek denetim bilgileri

**Kimlik**: nıst SP 800-53 R4 au-3 (1) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1108-denetim Içerikleri, \| ek denetim bilgileri](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9ad559e-c12d-415e-9a78-e50fdd7da7ba) |Microsoft bu denetim ve sorumluluk denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1108.json) |

### <a name="content-of-audit-records--centralized-management-of-planned-audit-record-content"></a>Denetim kayıtlarının içeriği | Planlı denetim kaydı Içeriğinin Merkezi Yönetimi

**Kimlik**: nıst SP 800-53 R4 au-3 (2) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[VM için Log Analytics çalışma alanını denetleme-rapor uyumsuzluğu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff47b5582-33ec-4c5c-87c0-b010a6b2e917) |İlke/girişim atamasında belirtilen Log Analytics çalışma alanına oturum açtıklarında VM 'Leri uyumsuz olarak raporlar. |denetlenmesini |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalytics_WorkspaceMismatch_VM_Audit.json) |
|[Listelenen sanal makine görüntüleri için Log Analytics Aracısı etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F32133ab0-ee4b-4b44-98d6-042180979d50) |Sanal makine görüntüsü tanımlanan listede değilse ve aracı yüklü değilse, sanal makineleri uyumsuz olarak raporlar. |Auditınotexists, devre dışı |[2.0.0-Önizleme](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalytics_OSImage_Audit.json) |
|[Log Analytics aracısının, listelenen sanal makine görüntüleri için sanal makine ölçek kümelerinde etkinleştirilmesi gerekir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5c3bc7b8-a64c-4e08-a9cd-7ff0f31e1138) |Sanal makine görüntüsü tanımlanmış listede değilse ve aracı yüklü değilse, sanal makine ölçek kümelerini uyumlu değil olarak raporlar. |Auditınotexists, devre dışı |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalytics_OSImage_VMSS_Audit.json) |
|[Microsoft yönetilen denetim 1109-denetim Içeriği, \| planlı denetim kaydı içeriğinin merkezi yönetimini kaydeder](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7d9ffa23-ad92-4d0d-b1f4-7db274cc2aec) |Microsoft bu denetim ve sorumluluk denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1109.json) |

### <a name="audit-storage-capacity"></a>Depolama kapasitesini denetleme

**Kimlik**: nıst SP 800-53 R4 au-4 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1110-depolama kapasitesini denetleme](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6182bfa7-0f2a-43f5-834a-a2ddf31c13c7) |Microsoft bu denetim ve sorumluluk denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1110.json) |

### <a name="response-to-audit-processing-failures"></a>Denetim Işleme hatalarının yanıtı

**Kimlik**: nıst SP 800-53 R4 au-5 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Gelişmiş veri güvenliği SQL yönetilen örneği üzerinde etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |Gelişmiş veri güvenliği olmadan her SQL yönetilen örneğini denetleyin. |Auditınotexists, devre dışı |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[Gelişmiş veri güvenliği SQL sunucularınızda etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |Gelişmiş veri güvenliği olmadan SQL sunucularını denetleme |Auditınotexists, devre dışı |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |
|[Tanılama ayarını denetle](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7f89b1eb-583c-429a-8828-af049802c1d9) |Seçili kaynak türleri için tanılama ayarını denetle |AuditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/DiagnosticSettingsForTypes_Audit.json) |
|[SQL Server üzerinde denetim etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa6fb4358-5bf4-4ad7-ba82-2cd2f41ce5e9) |SQL Server denetim, sunucudaki tüm veritabanları genelinde veritabanı etkinliklerini izlemek ve bir denetim günlüğüne kaydetmek için etkinleştirilmelidir. |Auditınotexists, devre dışı |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditing_Audit.json) |
|[Microsoft yönetilen denetim 1111-Işleme başarısızlıklarını denetleme yanıtı](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F21de687c-f15e-4e51-bf8d-f35c8619965b) |Microsoft bu denetim ve sorumluluk denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1111.json) |
|[Microsoft yönetilen denetim 1112-Işleme başarısızlıklarını denetleme yanıtı](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd530aad8-4ee2-45f4-b234-c061dae683c0) |Microsoft bu denetim ve sorumluluk denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1112.json) |

### <a name="response-to-audit-processing-failures--audit-storage-capacity"></a>Işleme başarısızlıklarını denetleme yanıtı | Depolama kapasitesini denetleme

**Kimlik**: nıst SP 800-53 R4 au-5 (1) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1113-denetim Işleme hatalarının yanıtı \| Denetim depolama kapasitesi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F562afd61-56be-4313-8fe4-b9564aa4ba7d) |Microsoft bu denetim ve sorumluluk denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1113.json) |

### <a name="response-to-audit-processing-failures--real-time-alerts"></a>Işleme başarısızlıklarını denetleme yanıtı | Real-Time uyarılar

**Kimlik**: nıst SP 800-53 R4 au-5 (2) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1114-Işleme başarısızlıklarını denetleme yanıtı \| Real-Time uyarıları](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4c090801-59bc-4454-bb33-e0455133486a) |Microsoft bu denetim ve sorumluluk denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1114.json) |

### <a name="audit-review-analysis-and-reporting"></a>Denetim Inceleme, analiz ve raporlama

**Kimlik**: nıst SP 800-53 R4 au-6 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1115-denetim Incelemesi, analiz ve raporlama](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b653845-2ad9-4e09-a4f3-5a7c1d78353d) |Microsoft bu denetim ve sorumluluk denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1115.json) |
|[Microsoft yönetilen denetim 1116-denetim Incelemesi, analiz ve raporlama](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5e47bc51-35d1-44b8-92af-e2f2d8b67635) |Microsoft bu denetim ve sorumluluk denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1116.json) |

### <a name="audit-review-analysis-and-reporting--process-integration"></a>Denetim Inceleme, analiz ve raporlama | İşlem tümleştirmesi

**Kimlik**: nıst SP 800-53 R4 au-6 (1) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1117-denetim Inceleme, analiz ve raporlama \| Işlemi tümleştirmesi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7fbfe680-6dbb-4037-963c-a621c5635902) |Microsoft bu denetim ve sorumluluk denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1117.json) |

### <a name="audit-review-analysis-and-reporting--correlate-audit-repositories"></a>Denetim Inceleme, analiz ve raporlama | Denetim depolarının ilişkilendirilmesi

**Kimlik**: nıst SP 800-53 R4 au-6 (3) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1118-denetim Inceleme, analiz ve raporlama Ile \| bağıntılı denetim depoları](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa96f743d-a195-420d-983a-08aa06bc441e) |Microsoft bu denetim ve sorumluluk denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1118.json) |

### <a name="audit-review-analysis-and-reporting--central-review-and-analysis"></a>Denetim Inceleme, analiz ve raporlama | Merkezi Inceleme ve analiz

**Kimlik**: nıst SP 800-53 R4 au-6 (4) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[VM için Log Analytics çalışma alanını denetleme-rapor uyumsuzluğu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff47b5582-33ec-4c5c-87c0-b010a6b2e917) |İlke/girişim atamasında belirtilen Log Analytics çalışma alanına oturum açtıklarında VM 'Leri uyumsuz olarak raporlar. |denetlenmesini |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalytics_WorkspaceMismatch_VM_Audit.json) |
|[Listelenen sanal makine görüntüleri için Log Analytics Aracısı etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F32133ab0-ee4b-4b44-98d6-042180979d50) |Sanal makine görüntüsü tanımlanan listede değilse ve aracı yüklü değilse, sanal makineleri uyumsuz olarak raporlar. |Auditınotexists, devre dışı |[2.0.0-Önizleme](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalytics_OSImage_Audit.json) |
|[Log Analytics aracısının, listelenen sanal makine görüntüleri için sanal makine ölçek kümelerinde etkinleştirilmesi gerekir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5c3bc7b8-a64c-4e08-a9cd-7ff0f31e1138) |Sanal makine görüntüsü tanımlanmış listede değilse ve aracı yüklü değilse, sanal makine ölçek kümelerini uyumlu değil olarak raporlar. |Auditınotexists, devre dışı |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalytics_OSImage_VMSS_Audit.json) |
|[Microsoft yönetilen denetim 1119-denetim Inceleme, analiz ve raporlama \| Merkezi Inceleme ve analiz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F845f6359-b764-4b40-b579-657aefe23c44) |Microsoft bu denetim ve sorumluluk denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1119.json) |

### <a name="audit-review-analysis-and-reporting--integration--scanning-and-monitoring-capabilities"></a>Denetim Inceleme, analiz ve raporlama | Tümleştirme/tarama ve Izleme özellikleri

**Kimlik**: nıst SP 800-53 R4 au-6 (5) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1120-denetim Inceleme, analiz ve raporlama \| tümleştirmesi/tarama ve Izleme özellikleri](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc69b870e-857b-458b-af02-bb234f7a00d3) |Microsoft bu denetim ve sorumluluk denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1120.json) |

### <a name="audit-review-analysis-and-reporting--correlation-with-physical-monitoring"></a>Denetim Inceleme, analiz ve raporlama | Fiziksel Izleme Ile bağıntı

**Kimlik**: nıst SP 800-53 R4 au-6 (6) **sahipliği**: Microsoft

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1121-denetim Inceleme, analiz ve raporlama \| bağıntısını fiziksel izlemeye yönelik olarak](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc72b0eb9-1fc2-44e5-a866-e7cb0532f7c1) |Microsoft bu denetim ve sorumluluk denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1121.json) |

### <a name="audit-review-analysis-and-reporting--permitted-actions"></a>Denetim Inceleme, analiz ve raporlama | İzin verilen eylemler

**Kimlik**: nıst SP 800-53 R4 au-6 (7) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1122-denetim Incelemesi, analiz ve raporlama \| izin verilen eylemler](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F243ec95e-800c-49d4-ba52-1fdd9f6b8b57) |Microsoft bu denetim ve sorumluluk denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1122.json) |

### <a name="audit-review-analysis-and-reporting--audit-level-adjustment"></a>Denetim Inceleme, analiz ve raporlama | Denetim düzeyi ayarlaması

**Kimlik**: nıst SP 800-53 R4 au-6 (10) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1123-denetim Inceleme, analiz ve raporlama \| Denetim düzeyi ayarlama](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F03996055-37a4-45a5-8b70-3f1caa45f87d) |Microsoft bu denetim ve sorumluluk denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1123.json) |

### <a name="audit-reduction-and-report-generation"></a>Denetim azaltma ve rapor oluşturma

**Kimlik**: nıst SP 800-53 R4 au-7 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1124-denetim azaltma ve rapor oluşturma](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc10152dd-78f8-4335-ae2d-ad92cc028da4) |Microsoft bu denetim ve sorumluluk denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1124.json) |
|[Microsoft yönetilen denetim 1125-denetim azaltma ve rapor oluşturma](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc6ce745a-670e-47d3-a6c4-3cfe5ef00c10) |Microsoft bu denetim ve sorumluluk denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1125.json) |

### <a name="audit-reduction-and-report-generation--automatic-processing"></a>Denetim azaltma ve rapor oluşturma | Otomatik Işleme

**Kimlik**: nıst SP 800-53 R4 au-7 (1) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1126-denetim azaltma ve rapor oluşturma \| otomatik işleme](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7f37f71b-420f-49bf-9477-9c0196974ecf) |Microsoft bu denetim ve sorumluluk denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1126.json) |

### <a name="time-stamps"></a>Zaman damgaları

**Kimlik**: nıst SP 800-53 R4 au-8 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1127-zaman damgaları](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3ce328db-aef3-48ed-9f81-2ab7cf839c66) |Microsoft bu denetim ve sorumluluk denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1127.json) |
|[Microsoft yönetilen denetim 1128-zaman damgaları](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef212163-3bc4-4e86-bcf8-705127086393) |Microsoft bu denetim ve sorumluluk denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1128.json) |

### <a name="time-stamps--synchronization-with-authoritative-time-source"></a>Zaman damgaları | Yetkili saat kaynağıyla eşitleme

**Kimlik**: nıst SP 800-53 R4 au-8 (1) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1129- \| yetkili saat kaynağıyla zaman damgası eşitlemesi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F71bb965d-4047-4623-afd4-b8189a58df5d) |Microsoft bu denetim ve sorumluluk denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1129.json) |
|[Microsoft yönetilen denetim 1130- \| yetkili saat kaynağıyla zaman damgası eşitlemesi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffd7c4c1d-51ee-4349-9dab-89a7f8c8d102) |Microsoft bu denetim ve sorumluluk denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1130.json) |

### <a name="protection-of-audit-information"></a>Denetim bilgilerinin korunması

**Kimlik**: nıst SP 800-53 R4 au-9 **sahipliği**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1131-denetim bilgilerinin korunması](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb472a17e-c2bc-493f-b50b-42d55a346962) |Microsoft bu denetim ve sorumluluk denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1131.json) |

### <a name="protection-of-audit-information--audit-backup-on-separate-physical-systems--components"></a>Denetim bilgilerini koruma | Ayrı fiziksel sistemlere/bileşenlere yedeklemeyi denetleme

**Kimlik**: nıst SP 800-53 R4 au-9 (2) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1132-denetim bilgilerinin \| ayrı fiziksel sistemlere/bileşenlere korunması denetim yedeklemesi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F05938e10-cdbd-4a54-9b2b-1cbcfc141ad0) |Microsoft bu denetim ve sorumluluk denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1132.json) |

### <a name="protection-of-audit-information--cryptographic-protection"></a>Denetim bilgilerini koruma | Şifreleme koruması

**Kimlik**: nıst SP 800-53 R4 au-9 (3) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1133-denetim bilgileri \| şifreleme koruması koruması](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F90b60a09-133d-45bc-86ef-b206a6134bbe) |Microsoft bu denetim ve sorumluluk denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1133.json) |

### <a name="protection-of-audit-information--access-by-subset-of-privileged-users"></a>Denetim bilgilerini koruma | Ayrıcalıklı kullanıcıların alt kümesine göre erişim

**Kimlik**: nıst SP 800-53 R4 au-9 (4) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1134- \| ayrıcalıklı kullanıcıların alt kümesiyle denetim bilgileri erişiminin korunması](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4e95f70e-181c-4422-9da2-43079710c789) |Microsoft bu denetim ve sorumluluk denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1134.json) |

### <a name="non-repudiation"></a>İnkar Edilemez

**Kimlik**: nıst SP 800-53 R4 au-10 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1135-Red olmayan](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9c308b6b-2429-4b97-86cf-081b8e737b04) |Microsoft bu denetim ve sorumluluk denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1135.json) |

### <a name="audit-record-retention"></a>Denetim kaydı saklama

**Kimlik**: nıst SP 800-53 R4 au-11 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1136-kayıt bekletme denetimi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F97ed5bac-a92f-4f6d-a8ed-dc094723597c) |Microsoft bu denetim ve sorumluluk denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1136.json) |

### <a name="audit-generation"></a>Denetim oluşturma

**Kimlik**: nıst SP 800-53 R4 Au-12 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Gelişmiş veri güvenliği SQL yönetilen örneği üzerinde etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |Gelişmiş veri güvenliği olmadan her SQL yönetilen örneğini denetleyin. |Auditınotexists, devre dışı |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[Gelişmiş veri güvenliği SQL sunucularınızda etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |Gelişmiş veri güvenliği olmadan SQL sunucularını denetleme |Auditınotexists, devre dışı |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |
|[Tanılama ayarını denetle](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7f89b1eb-583c-429a-8828-af049802c1d9) |Seçili kaynak türleri için tanılama ayarını denetle |AuditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/DiagnosticSettingsForTypes_Audit.json) |
|[VM için Log Analytics çalışma alanını denetleme-rapor uyumsuzluğu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff47b5582-33ec-4c5c-87c0-b010a6b2e917) |İlke/girişim atamasında belirtilen Log Analytics çalışma alanına oturum açtıklarında VM 'Leri uyumsuz olarak raporlar. |denetlenmesini |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalytics_WorkspaceMismatch_VM_Audit.json) |
|[SQL Server üzerinde denetim etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa6fb4358-5bf4-4ad7-ba82-2cd2f41ce5e9) |SQL Server denetim, sunucudaki tüm veritabanları genelinde veritabanı etkinliklerini izlemek ve bir denetim günlüğüne kaydetmek için etkinleştirilmelidir. |Auditınotexists, devre dışı |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditing_Audit.json) |
|[Listelenen sanal makine görüntüleri için Log Analytics Aracısı etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F32133ab0-ee4b-4b44-98d6-042180979d50) |Sanal makine görüntüsü tanımlanan listede değilse ve aracı yüklü değilse, sanal makineleri uyumsuz olarak raporlar. |Auditınotexists, devre dışı |[2.0.0-Önizleme](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalytics_OSImage_Audit.json) |
|[Log Analytics aracısının, listelenen sanal makine görüntüleri için sanal makine ölçek kümelerinde etkinleştirilmesi gerekir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5c3bc7b8-a64c-4e08-a9cd-7ff0f31e1138) |Sanal makine görüntüsü tanımlanmış listede değilse ve aracı yüklü değilse, sanal makine ölçek kümelerini uyumlu değil olarak raporlar. |Auditınotexists, devre dışı |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalytics_OSImage_VMSS_Audit.json) |
|[Microsoft yönetilen denetim 1137-denetim oluşturma](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4344df62-88ab-4637-b97b-bcaf2ec97e7c) |Microsoft bu denetim ve sorumluluk denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1137.json) |
|[Microsoft yönetilen denetim 1138-denetim oluşturma](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9c284fc0-268a-4f29-af44-3c126674edb4) |Microsoft bu denetim ve sorumluluk denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1138.json) |
|[Microsoft yönetilen denetim 1139-denetim oluşturma](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4ed62522-de00-4dda-9810-5205733d2f34) |Microsoft bu denetim ve sorumluluk denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1139.json) |

### <a name="audit-generation--system-wide--time-correlated-audit-trail"></a>Denetim oluşturma | System-Wide/Time-Correlated denetim Izi

**Kimlik**: nıst SP 800-53 R4 Au-12 (1) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1140-denetim oluşturma \| System-Wide/Time-Correlated denetim izi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F90d8b8ad-8ee3-4db7-913f-2a53fcff5316) |Microsoft bu denetim ve sorumluluk denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1140.json) |

### <a name="audit-generation--changes-by-authorized-individuals"></a>Denetim oluşturma | Yetkili kişiler tarafından yapılan değişiklikler

**Kimlik**: nıst SP 800-53 R4 Au-12 (3) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1141- \| yetkili kişiler tarafından yapılan denetim oluşturma değişiklikleri](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6fdefbf4-93e7-4513-bc95-c1858b7093e0) |Microsoft bu denetim ve sorumluluk denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1141.json) |

## <a name="security-assessment-and-authorization"></a>Güvenlik değerlendirmesi ve yetkilendirme

### <a name="security-assessment-and-authorization-policy-and-procedures"></a>Güvenlik değerlendirmesi ve yetkilendirme Ilkesi ve yordamları

**Kimlik**: nıst SP 800-53 R4 CA-1 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1142-güvenlik değerlendirmesi ve yetkilendirme Ilkesi ve yordamları](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F01524fa8-4555-48ce-ba5f-c3b8dcef5147) |Microsoft bu güvenlik değerlendirmesi ve Yetkilendirme denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1142.json) |
|[Microsoft yönetilen denetim 1143-güvenlik değerlendirmesi ve yetkilendirme Ilkesi ve yordamları](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7c6de11b-5f51-4f7c-8d83-d2467c8a816e) |Microsoft bu güvenlik değerlendirmesi ve Yetkilendirme denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1143.json) |

### <a name="security-assessments"></a>Güvenlik değerlendirmeleri

**Kimlik**: nıst SP 800-53 R4 CA-2 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1144-güvenlik değerlendirmeleri](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2fa15ff1-a693-4ee4-b094-324818dc9a51) |Microsoft bu güvenlik değerlendirmesi ve Yetkilendirme denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1144.json) |
|[Microsoft yönetilen denetim 1145-güvenlik değerlendirmeleri](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa0724970-9c75-4a64-a225-a28002953f28) |Microsoft bu güvenlik değerlendirmesi ve Yetkilendirme denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1145.json) |
|[Microsoft yönetilen denetim 1146-güvenlik değerlendirmeleri](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdd83410c-ecb6-4547-8f14-748c3cbdc7ac) |Microsoft bu güvenlik değerlendirmesi ve Yetkilendirme denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1146.json) |
|[Microsoft yönetilen denetim 1147-güvenlik değerlendirmeleri](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8fef824a-29a8-4a4c-88fc-420a39c0d541) |Microsoft bu güvenlik değerlendirmesi ve Yetkilendirme denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1147.json) |

### <a name="security-assessments--independent-assessors"></a>Güvenlik değerlendirmeleri | Bağımsız denetçiler

**Kimlik**: nıst SP 800-53 R4 CA-2 (1) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1148-güvenlik değerlendirmeleri \| bağımsız denetçiler](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F28e62650-c7c2-4786-bdfa-17edc1673902) |Microsoft bu güvenlik değerlendirmesi ve Yetkilendirme denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1148.json) |

### <a name="security-assessments--specialized-assessments"></a>Güvenlik değerlendirmeleri | Özel değerlendirmeler

**Kimlik**: nıst SP 800-53 R4 CA-2 (2) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1149-güvenlik değerlendirmesi \| özel değerlendirmeleri](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2e1b855b-a013-481a-aeeb-2bcb129fd35d) |Microsoft bu güvenlik değerlendirmesi ve Yetkilendirme denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1149.json) |

### <a name="security-assessments--external-organizations"></a>Güvenlik değerlendirmeleri | Dış kuruluşlar

**Kimlik**: nıst SP 800-53 R4 CA-2 (3) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1150-güvenlik değerlendirmesi \| dış kuruluşlar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd630429d-e763-40b1-8fba-d20ba7314afb) |Microsoft bu güvenlik değerlendirmesi ve Yetkilendirme denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1150.json) |

### <a name="system-interconnections"></a>Sistem bağlantıları

**Kimlik**: nıst SP 800-53 R4 CA-3 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1151-sistem içi bağlantılar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F347e3b69-7fb7-47df-a8ef-71a1a7b44bca) |Microsoft bu güvenlik değerlendirmesi ve Yetkilendirme denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1151.json) |
|[Microsoft yönetilen denetim 1152-sistem içi bağlantılar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbeff0acf-7e67-40b2-b1ca-1a0e8205cf1b) |Microsoft bu güvenlik değerlendirmesi ve Yetkilendirme denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1152.json) |
|[Microsoft yönetilen denetim 1153-sistem içi bağlantılar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F61cf3125-142c-4754-8a16-41ab4d529635) |Microsoft bu güvenlik değerlendirmesi ve Yetkilendirme denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1153.json) |

### <a name="system-interconnections--unclassified-non-national-security-system-connections"></a>Sistem bağlantıları | Sınıflandırılmamış ulusal olmayan güvenlik sistemi bağlantıları

**Kimlik**: nıst SP 800-53 R4 CA-3 (3) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1154-sistem içi bağlantılar \| Sınıflandırılmamış ulusal güvenlik sistemi bağlantıları](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe757ceb9-93b3-45fe-a4f4-f43f64f1ac5a) |Microsoft bu güvenlik değerlendirmesi ve Yetkilendirme denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1154.json) |

### <a name="system-interconnections--restrictions-on-external-system-connections"></a>Sistem bağlantıları | Dış Sistem bağlantılarında kısıtlamalar

**Kimlik**: nıst SP 800-53 R4 CA-3 (5) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1155- \| dış sistem bağlantılarında sistem bağlantıları arası kısıtlamalar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4d33f9f1-12d0-46ad-9fbd-8f8046694977) |Microsoft bu güvenlik değerlendirmesi ve Yetkilendirme denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1155.json) |

### <a name="plan-of-action-and-milestones"></a>Eylem ve kilometre taşları planı

**Kimlik**: nıst SP 800-53 R4 CA-5 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1156-eylem ve kilometre taşları planı](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4d52e864-9a3b-41ee-8f03-520815fe5378) |Microsoft bu güvenlik değerlendirmesi ve Yetkilendirme denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1156.json) |
|[Microsoft yönetilen denetim 1157-eylem ve kilometre taşları planı](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F15495367-cf68-464c-bbc3-f53ca5227b7a) |Microsoft bu güvenlik değerlendirmesi ve Yetkilendirme denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1157.json) |

### <a name="security-authorization"></a>Güvenlik yetkilendirmesi

**Kimlik**: nıst SP 800-53 R4 CA-6 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1158-güvenlik yetkilendirmesi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffff50cf2-28eb-45b4-b378-c99412688907) |Microsoft bu güvenlik değerlendirmesi ve Yetkilendirme denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1158.json) |
|[Microsoft yönetilen denetim 1159-güvenlik yetkilendirmesi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0925f098-7877-450b-8ba4-d1e55f2d8795) |Microsoft bu güvenlik değerlendirmesi ve Yetkilendirme denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1159.json) |
|[Microsoft yönetilen denetim 1160-güvenlik yetkilendirmesi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3e797ca6-2aa8-4333-b335-7036f1110c05) |Microsoft bu güvenlik değerlendirmesi ve Yetkilendirme denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1160.json) |

### <a name="continuous-monitoring"></a>Sürekli Izleme

**Kimlik**: nıst SP 800-53 R4 CA-7 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1161-sürekli Izleme](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe2f8f6c6-dde4-436b-a79d-bc50e129eb3a) |Microsoft bu güvenlik değerlendirmesi ve Yetkilendirme denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1161.json) |
|[Microsoft yönetilen denetim 1162-sürekli Izleme](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5770f3d6-8c2b-4f6f-bf0e-c8c8fc36d592) |Microsoft bu güvenlik değerlendirmesi ve Yetkilendirme denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1162.json) |
|[Microsoft yönetilen denetim 1163-sürekli Izleme](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F961663a1-8a91-4e59-b6f5-1eee57c0f49c) |Microsoft bu güvenlik değerlendirmesi ve Yetkilendirme denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1163.json) |
|[Microsoft yönetilen denetim 1164-sürekli Izleme](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fb8d3ce-9e96-481c-9c68-88d4e3019310) |Microsoft bu güvenlik değerlendirmesi ve Yetkilendirme denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1164.json) |
|[Microsoft yönetilen denetim 1165-sürekli Izleme](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47e10916-6c9e-446b-b0bd-ff5fd439d79d) |Microsoft bu güvenlik değerlendirmesi ve Yetkilendirme denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1165.json) |
|[Microsoft yönetilen denetim 1166-sürekli Izleme](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbb02733d-3cc5-4bb0-a6cd-695ba2c2272e) |Microsoft bu güvenlik değerlendirmesi ve Yetkilendirme denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1166.json) |
|[Microsoft yönetilen denetim 1167-sürekli Izleme](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcbb2be76-4891-430b-95a7-ca0b0a3d1300) |Microsoft bu güvenlik değerlendirmesi ve Yetkilendirme denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1167.json) |

### <a name="continuous-monitoring--independent-assessment"></a>Sürekli Izleme | Bağımsız değerlendirme

**Kimlik**: nıst SP 800-53 R4 CA-7 (1) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1168-sürekli Izleme \| bağımsız değerlendirmesi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82409f9e-1f32-4775-bf07-b99d53a91b06) |Microsoft bu güvenlik değerlendirmesi ve Yetkilendirme denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1168.json) |

### <a name="continuous-monitoring--trend-analyses"></a>Sürekli Izleme | Eğilim analizleri

**Kimlik**: nıst SP 800-53 R4 CA-7 (3) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1169-sürekli Izleme \| eğilimi analizleri](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe7ba2cb3-5675-4468-8b50-8486bdd998a5) |Microsoft bu güvenlik değerlendirmesi ve Yetkilendirme denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1169.json) |

### <a name="penetration-testing"></a>Sızma Testi

**Kimlik**: nıst SP 800-53 R4 CA-8 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1170-Penetasyon testi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8b78b9b3-ee3c-48e0-a243-ed6dba5b7a12) |Microsoft bu güvenlik değerlendirmesi ve Yetkilendirme denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1170.json) |

### <a name="penetration-testing--independent-penetration-agent-or-team"></a>Sızma testi | Bağımsız sızma Aracısı veya ekibi

**Kimlik**: nıst SP 800-53 R4 CA-8 (1) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1171-Penetasyon testi \| bağımsız aracı veya ekibi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d4820bc-8b61-4982-9501-2123cb776c00) |Microsoft bu güvenlik değerlendirmesi ve Yetkilendirme denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1171.json) |

### <a name="internal-system-connections"></a>İç sistem bağlantıları

**Kimlik**: nıst SP 800-53 R4 CA-9 **sahipliği**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1172-Iç sistem bağlantıları](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb43e946e-a4c8-4b92-8201-4a39331db43c) |Microsoft bu güvenlik değerlendirmesi ve Yetkilendirme denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1172.json) |
|[Microsoft yönetilen denetim 1173-Iç sistem bağlantıları](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4aff9e7-2e60-46fa-86be-506b79033fc5) |Microsoft bu güvenlik değerlendirmesi ve Yetkilendirme denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1173.json) |

## <a name="configuration-management"></a>Yapılandırma Yönetimi

### <a name="configuration-management-policy-and-procedures"></a>Yapılandırma yönetimi Ilkesi ve yordamları

**Kimlik**: nıst SP 800-53 R4 cm-1 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1174-yapılandırma yönetimi Ilkesi ve yordamları](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F42a9a714-8fbb-43ac-b115-ea12d2bd652f) |Microsoft bu yapılandırma yönetimi denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1174.json) |
|[Microsoft yönetilen denetim 1175-yapılandırma yönetimi Ilkesi ve yordamları](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6dab4254-c30d-4bb7-ae99-1d21586c063c) |Microsoft bu yapılandırma yönetimi denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1175.json) |

### <a name="baseline-configuration"></a>Taban çizgisi yapılandırması

**Kimlik**: nıst SP 800-53 R4 cm-2 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1176-taban çizgisi yapılandırması](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc30690a5-7bf3-467f-b0cd-ef5c7c7449cd) |Microsoft bu yapılandırma yönetimi denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1176.json) |

### <a name="baseline-configuration--reviews-and-updates"></a>Temel yapılandırma | İncelemeler ve güncelleştirmeler

**Kimlik**: nıst SP 800-53 R4 cm-2 (1) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1177-taban çizgisi yapılandırma \| Incelemeleri ve güncelleştirmeleri](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F63dbc7a8-e20b-4d38-b857-a7f6c0cd94bc) |Microsoft bu yapılandırma yönetimi denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1177.json) |
|[Microsoft yönetilen denetim 1178-taban çizgisi yapılandırma \| Incelemeleri ve güncelleştirmeleri](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7818b8f4-47c6-441a-90ae-12ce04e99893) |Microsoft bu yapılandırma yönetimi denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1178.json) |
|[Microsoft yönetilen denetim 1179-taban çizgisi yapılandırma \| Incelemeleri ve güncelleştirmeleri](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3f9ce557-c8ab-4e6c-bb2c-9b8ed002c46c) |Microsoft bu yapılandırma yönetimi denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1179.json) |

### <a name="baseline-configuration--automation-support-for-accuracy--currency"></a>Temel yapılandırma | Doğruluk/para birimi için Otomasyon desteği

**Kimlik**: nıst SP 800-53 R4 cm-2 (2) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1180- \| doğruluk/para birimi Için temel yapılandırma Otomasyonu desteği](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F874e7880-a067-42a7-bcbe-1a340f54c8cc) |Microsoft bu yapılandırma yönetimi denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1180.json) |

### <a name="baseline-configuration--retention-of-previous-configurations"></a>Temel yapılandırma | Önceki yapılandırmaların saklanması

**Kimlik**: nıst SP 800-53 R4 cm-2 (3) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1181- \| önceki yapılandırmaların temel yapılandırma saklama](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F21839937-d241-4fa5-95c6-b669253d9ab9) |Microsoft bu yapılandırma yönetimi denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1181.json) |

### <a name="baseline-configuration--configure-systems-components-or-devices-for-high-risk-areas"></a>Temel yapılandırma | High-Risk alanlara yönelik sistemleri, bileşenleri veya aygıtları yapılandırma

**Kimlik**: nıst SP 800-53 R4 cm-2 (7) **sahiplik**: Microsoft

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1182-temel yapılandırma \| sistemleri, bileşenleri veya High-Risk alanlara yönelik cihazları yapılandırın](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f34f554-da4b-4786-8d66-7915c90893da) |Microsoft bu yapılandırma yönetimi denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1182.json) |
|[Microsoft yönetilen denetim 1183-temel yapılandırma \| sistemleri, bileşenleri veya High-Risk alanlara yönelik cihazları yapılandırın](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5352e3e0-e63a-452e-9e5f-9c1d181cff9c) |Microsoft bu yapılandırma yönetimi denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1183.json) |

### <a name="configuration-change-control"></a>Yapılandırma değişikliği denetimi

**Kimlik**: nıst SP 800-53 R4 cm-3 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1184-yapılandırma değişikliği denetimi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F13579d0e-0ab0-4b26-b0fb-d586f6d7ed20) |Microsoft bu yapılandırma yönetimi denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1184.json) |
|[Microsoft yönetilen denetim 1185-yapılandırma değişikliği denetimi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6420cd73-b939-43b7-9d99-e8688fea053c) |Microsoft bu yapılandırma yönetimi denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1185.json) |
|[Microsoft yönetilen denetim 1186-yapılandırma değişikliği denetimi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb95ba3bd-4ded-49ea-9d10-c6f4b680813d) |Microsoft bu yapılandırma yönetimi denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1186.json) |
|[Microsoft yönetilen denetim 1187-yapılandırma değişikliği denetimi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9f2b2f9e-4ba6-46c3-907f-66db138b6f85) |Microsoft bu yapılandırma yönetimi denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1187.json) |
|[Microsoft yönetilen denetim 1188-yapılandırma değişikliği denetimi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbb20548a-c926-4e4d-855c-bcddc6faf95e) |Microsoft bu yapılandırma yönetimi denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1188.json) |
|[Microsoft yönetilen denetim 1189-yapılandırma değişikliği denetimi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fee45e02a-4140-416c-82c4-fecfea660b9d) |Microsoft bu yapılandırma yönetimi denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1189.json) |
|[Microsoft yönetilen denetim 1190-yapılandırma değişikliği denetimi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc66a3d1e-465b-4f28-9da5-aef701b59892) |Microsoft bu yapılandırma yönetimi denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1190.json) |

### <a name="configuration-change-control--automated-document--notification--prohibition-of-changes"></a>Yapılandırma değişikliği denetimi | Otomatik belge/bildirim/değişiklik değişiklikleri

**Kimlik**: nıst SP 800-53 R4 cm-3 (1) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1191-yapılandırma değişikliği denetimi \| Otomatik belge/bildirim/değişiklik değişiklikleri](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7f26a61b-a74d-467c-99cf-63644db144f7) |Microsoft bu yapılandırma yönetimi denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1191.json) |
|[Microsoft yönetilen denetim 1192-yapılandırma değişikliği denetimi \| Otomatik belge/bildirim/değişiklik değişiklikleri](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4ebd97f7-b105-4f50-8daf-c51465991240) |Microsoft bu yapılandırma yönetimi denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1192.json) |
|[Microsoft yönetilen denetim 1193-yapılandırma değişikliği denetimi \| Otomatik belge/bildirim/değişiklik değişiklikleri](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff5fd629f-3075-4cae-ab53-bad65495a4ac) |Microsoft bu yapılandırma yönetimi denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1193.json) |
|[Microsoft yönetilen denetim 1194-yapılandırma değişikliği denetimi \| Otomatik belge/bildirim/değişiklik değişiklikleri](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbc34667f-397e-4a65-9b72-d0358f0b6b09) |Microsoft bu yapılandırma yönetimi denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1194.json) |
|[Microsoft yönetilen denetim 1195-yapılandırma değişikliği denetimi \| Otomatik belge/bildirim/değişiklik değişiklikleri](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd1e1d65c-1013-4484-bd54-991332e6a0d2) |Microsoft bu yapılandırma yönetimi denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1195.json) |
|[Microsoft yönetilen denetim 1196-yapılandırma değişikliği denetimi \| Otomatik belge/bildirim/değişiklik değişiklikleri](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4e7f4ea4-dd62-44f6-8886-ac6137cf52b0) |Microsoft bu yapılandırma yönetimi denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1196.json) |

### <a name="configuration-change-control--test--validate--document-changes"></a>Yapılandırma değişikliği denetimi | Test/doğrulama/belge değişiklikleri

**Kimlik**: nıst SP 800-53 R4 cm-3 (2) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1197-yapılandırma değiştirme denetimi \| test/doğrulama/belge değişiklikleri](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa20d2eaa-88e2-4907-96a2-8f3a05797e5c) |Microsoft bu yapılandırma yönetimi denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1197.json) |

### <a name="configuration-change-control--security-representative"></a>Yapılandırma değişikliği denetimi | Güvenlik temsilcisi

**Kimlik**: nıst SP 800-53 R4 cm-3 (4) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1198-yapılandırma değişikliği denetim \| Güvenlik temsilcisi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff56be5c3-660b-4c61-9078-f67cf072c356) |Microsoft bu yapılandırma yönetimi denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1198.json) |

### <a name="configuration-change-control--cryptography-management"></a>Yapılandırma değişikliği denetimi | Şifreleme yönetimi

**Kimlik**: nıst SP 800-53 R4 cm-3 (6) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1199-yapılandırma değiştirme denetim \| şifreleme yönetimi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa9a08d1c-09b1-48f1-90ea-029bbdf7111e) |Microsoft bu yapılandırma yönetimi denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1199.json) |

### <a name="security-impact-analysis"></a>Güvenlik etkisi analizi

**Kimlik**: nıst SP 800-53 R4 cm-4 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1200-güvenlik etkisi analizi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe98fe9d7-2ed3-44f8-93b7-24dca69783ff) |Microsoft bu yapılandırma yönetimi denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1200.json) |

### <a name="security-impact-analysis--separate-test-environments"></a>Güvenlik etkisi analizi | Ayrı test ortamları

**Kimlik**: nıst SP 800-53 R4 cm-4 (1) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1201-güvenlik etkisi analizi \| ayrı test ortamları](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7daef997-fdd3-461b-8807-a608a6dd70f1) |Microsoft bu yapılandırma yönetimi denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1201.json) |

### <a name="access-restrictions-for-change"></a>Değişiklik için erişim kısıtlamaları

**Kimlik**: nıst SP 800-53 R4 cm-5 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft tarafından yönetilen denetim 1202-değişiklik Için erişim kısıtlamaları](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F40a2a83b-74f2-4c02-ae65-f460a5d2792a) |Microsoft bu yapılandırma yönetimi denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1202.json) |

### <a name="access-restrictions-for-change--automated-access-enforcement--auditing"></a>Değişiklik için erişim kısıtlamaları | Otomatik erişim zorlaması/denetim

**Kimlik**: nıst SP 800-53 R4 cm-5 (1) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1203-değişiklik \| otomatik erişim zorlaması/denetim Için erişim kısıtlamaları](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9012d14-e3e6-4d7b-b926-9f37b5537066) |Microsoft bu yapılandırma yönetimi denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1203.json) |

### <a name="access-restrictions-for-change--review-system-changes"></a>Değişiklik için erişim kısıtlamaları | Sistem değişikliklerini gözden geçirme

**Kimlik**: nıst SP 800-53 R4 cm-5 (2) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1204-değişiklik \| Gözden geçirme sistem değişiklikleri Için erişim kısıtlamaları](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0f4f6750-d1ab-4a4c-8dfd-af3237682665) |Microsoft bu yapılandırma yönetimi denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1204.json) |

### <a name="access-restrictions-for-change--signed-components"></a>Değişiklik için erişim kısıtlamaları | İmzalı bileşenler

**Kimlik**: nıst SP 800-53 R4 cm-5 (3) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1205-imzalı bileşenleri Değiştir Için erişim kısıtlamaları \|](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b070cab-0fb8-4e48-ad29-fc90b4c2797c) |Microsoft bu yapılandırma yönetimi denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1205.json) |

### <a name="access-restrictions-for-change--limit-production--operational-privileges"></a>Değişiklik için erişim kısıtlamaları | Üretim/operasyonel ayrıcalıkları sınırla

**Kimlik**: nıst SP 800-53 R4 cm-5 (5) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1206-değişiklik \| sınırı üretim/Işletimsel ayrıcalıklar Için erişim kısıtlamaları](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe0de232d-02a0-4652-872d-88afb4ae5e91) |Microsoft bu yapılandırma yönetimi denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1206.json) |
|[Microsoft yönetilen denetim 1207-değişiklik \| sınırı üretim/Işletimsel ayrıcalıklar Için erişim kısıtlamaları](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8713a0ed-0d1e-4d10-be82-83dffb39830e) |Microsoft bu yapılandırma yönetimi denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1207.json) |

### <a name="configuration-settings"></a>Yapılandırma ayarları

**Kimlik**: nıst SP 800-53 R4 cm-6 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1208-yapılandırma ayarları](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5ea87673-d06b-456f-a324-8abcee5c159f) |Microsoft bu yapılandırma yönetimi denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1208.json) |
|[Microsoft yönetilen denetim 1209-yapılandırma ayarları](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fce669c31-9103-4552-ae9c-cdef4e03580d) |Microsoft bu yapılandırma yönetimi denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1209.json) |
|[Microsoft yönetilen denetim 1210-yapılandırma ayarları](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3502c968-c490-4570-8167-1476f955e9b8) |Microsoft bu yapılandırma yönetimi denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1210.json) |
|[Microsoft yönetilen denetim 1211-yapılandırma ayarları](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6a8b9dc8-6b00-4701-aa96-bba3277ebf50) |Microsoft bu yapılandırma yönetimi denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1211.json) |

### <a name="configuration-settings--automated-central-management--application--verification"></a>Yapılandırma ayarları | Otomatik Merkezi Yönetim/uygulama/doğrulama

**Kimlik**: nıst SP 800-53 R4 cm-6 (1) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1212-yapılandırma ayarları \| Otomatik Merkezi Yönetim/uygulama/doğrulama](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F56d970ee-4efc-49c8-8a4e-5916940d784c) |Microsoft bu yapılandırma yönetimi denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1212.json) |

### <a name="configuration-settings--respond-to-unauthorized-changes"></a>Yapılandırma ayarları | Yetkisiz değişikliklere yanıt verme

**Kimlik**: nıst SP 800-53 R4 cm-6 (2) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1213-yapılandırma ayarları \| yetkisiz değişikliklere yanıt verir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F81f11e32-a293-4a58-82cd-134af52e2318) |Microsoft bu yapılandırma yönetimi denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1213.json) |

### <a name="least-functionality"></a>En az Işlevsellik

**Kimlik**: nıst SP 800-53 R4 cm-7 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1214-en az Işlevsellik](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff714a4e2-b580-47b6-ae8c-f2812d3750f3) |Microsoft bu yapılandırma yönetimi denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1214.json) |
|[Microsoft yönetilen denetim 1215-en az Işlevsellik](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F88fc93e8-4745-4785-b5a5-b44bb92c44ff) |Microsoft bu yapılandırma yönetimi denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1215.json) |

### <a name="least-functionality--periodic-review"></a>En az Işlevsellik | Düzenli Inceleme

**Kimlik**: nıst SP 800-53 R4 cm-7 (1) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1216-en az Işlev \| düzenli incelemesi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7894fe6a-f5cb-44c8-ba90-c3f254ff9484) |Microsoft bu yapılandırma yönetimi denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1216.json) |
|[Microsoft yönetilen denetim 1217-en az Işlev \| düzenli incelemesi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fedea4f20-b02c-4115-be75-86c080e5c0ed) |Microsoft bu yapılandırma yönetimi denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1217.json) |

### <a name="least-functionality--prevent-program-execution"></a>En az Işlevsellik | Programın yürütülmesini engelle

**Kimlik**: nıst SP 800-53 R4 cm-7 (2) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Güvenli uygulamaları tanımlamaya yönelik Uyarlamalı uygulama denetimleri, makinelerinizde etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |Makinelerinizde çalışan bilinen güvenli uygulamaların listesini tanımlamak için uygulama denetimlerini etkinleştirin ve diğer uygulamalar çalıştırıldığında sizi uyarır. Bu, makinelerinizi kötü amaçlı yazılımlara karşı korumanıza yardımcı olur. Güvenlik Merkezi, kurallarınızı yapılandırma ve sürdürme sürecini basitleştirmek için makine öğrenimini kullanarak her makinede çalışan uygulamaları analiz eder ve bilinen güvenli uygulamalar listesini önerir. |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |
|[Microsoft yönetilen denetim 1218-en az Işlevsellik \| program yürütmeyi önler](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4a1d0394-b9f5-493e-9e83-563fd0ac4df8) |Microsoft bu yapılandırma yönetimi denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1218.json) |

### <a name="least-functionality--authorized-software--whitelisting"></a>En az Işlevsellik | Yetkili yazılım/beyaz listeleme

**Kimlik**: nıst SP 800-53 R4 cm-7 (5) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Güvenli uygulamaları tanımlamaya yönelik Uyarlamalı uygulama denetimleri, makinelerinizde etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |Makinelerinizde çalışan bilinen güvenli uygulamaların listesini tanımlamak için uygulama denetimlerini etkinleştirin ve diğer uygulamalar çalıştırıldığında sizi uyarır. Bu, makinelerinizi kötü amaçlı yazılımlara karşı korumanıza yardımcı olur. Güvenlik Merkezi, kurallarınızı yapılandırma ve sürdürme sürecini basitleştirmek için makine öğrenimini kullanarak her makinede çalışan uygulamaları analiz eder ve bilinen güvenli uygulamalar listesini önerir. |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |
|[Microsoft yönetilen denetim 1219-en az Işlev \| yetkili yazılım/beyaz listeleme](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2a39ac75-622b-4c88-9a3f-45b7373f7ef7) |Microsoft bu yapılandırma yönetimi denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1219.json) |
|[Microsoft yönetilen denetim 1220-en az Işlev \| yetkili yazılım/beyaz listeleme](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc40f31a7-81e1-4130-99e5-a02ceea2a1d6) |Microsoft bu yapılandırma yönetimi denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1220.json) |
|[Microsoft yönetilen denetim 1221-en az Işlev \| yetkili yazılım/beyaz listeleme](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22589a07-0007-486a-86ca-95355081ae2a) |Microsoft bu yapılandırma yönetimi denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1221.json) |

### <a name="information-system-component-inventory"></a>Bilgi sistemi bileşeni envanteri

**Kimlik**: nıst SP 800-53 R4 cm-8 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1222-bilgi sistemi bileşeni envanteri](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffb39e62f-6bda-4558-8088-ec03d5670914) |Microsoft bu yapılandırma yönetimi denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1222.json) |
|[Microsoft yönetilen denetim 1223-bilgi sistemi bileşeni envanteri](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F05a1bb01-ad5a-49c1-aad3-b0c893b2ec3a) |Microsoft bu yapılandırma yönetimi denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1223.json) |

### <a name="information-system-component-inventory--updates-during-installations--removals"></a>Bilgi sistemi bileşeni envanteri | Yüklemeler/kaldırma Işlemleri sırasında güncelleştirmeler

**Kimlik**: nıst SP 800-53 R4 cm-8 (1) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1224- \| yükleme/kaldırma Işlemleri sırasında bilgi sistemi bileşeni envanter güncelleştirmeleri](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F28cfa30b-7f72-47ce-ba3b-eed26c8d2c82) |Microsoft bu yapılandırma yönetimi denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1224.json) |

### <a name="information-system-component-inventory--automated-maintenance"></a>Bilgi sistemi bileşeni envanteri | Otomatik bakım

**Kimlik**: nıst SP 800-53 R4 cm-8 (2) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1225-bilgi sistemi bileşeni envanteri \| Otomatik bakım](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8d096fe0-f510-4486-8b4d-d17dc230980b) |Microsoft bu yapılandırma yönetimi denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1225.json) |

### <a name="information-system-component-inventory--automated-unauthorized-component-detection"></a>Bilgi sistemi bileşeni envanteri | Otomatik yetkisiz bileşen algılama

**Kimlik**: nıst SP 800-53 R4 cm-8 (3) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1226-bilgi sistemi bileşeni envanteri \| Otomatik yetkisiz bileşen algılama](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc158eb1c-ae7e-4081-8057-d527140c4e0c) |Microsoft bu yapılandırma yönetimi denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1226.json) |
|[Microsoft yönetilen denetim 1227-bilgi sistemi bileşeni envanteri \| Otomatik yetkisiz bileşen algılama](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F03b78f5e-4877-4303-b0f4-eb6583f25768) |Microsoft bu yapılandırma yönetimi denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1227.json) |

### <a name="information-system-component-inventory--accountability-information"></a>Bilgi sistemi bileşeni envanteri | Sorumluluk bilgileri

**Kimlik**: nıst SP 800-53 R4 cm-8 (4) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1228-bilgi sistemi bileşeni envanter \| sorumluluğu bilgileri](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F39c54140-5902-4079-8bb5-ad31936fe764) |Microsoft bu yapılandırma yönetimi denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1228.json) |

### <a name="information-system-component-inventory--no-duplicate-accounting-of-components"></a>Bilgi sistemi bileşeni envanteri | Yinelenen bileşen hesabı yok

**Kimlik**: nıst SP 800-53 R4 cm-8 (5) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1229-bilgi sistemi bileşeni envanteri \| yinelenen bileşen hesaplama yok](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F03752212-103c-4ab8-a306-7e813022ca9d) |Microsoft bu yapılandırma yönetimi denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1229.json) |

### <a name="configuration-management-plan"></a>Yapılandırma yönetimi planı

**Kimlik**: nıst SP 800-53 R4 cm-9 **sahipliği**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1230-yapılandırma yönetimi planı](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F11158848-f679-4e9b-aa7b-9fb07d945071) |Microsoft bu yapılandırma yönetimi denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1230.json) |
|[Microsoft yönetilen denetim 1231-yapılandırma yönetimi planı](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F244e0c05-cc45-4fe7-bf36-42dcf01f457d) |Microsoft bu yapılandırma yönetimi denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1231.json) |
|[Microsoft yönetilen denetim 1232-yapılandırma yönetimi planı](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F396ba986-eac1-4d6d-85c4-d3fda6b78272) |Microsoft bu yapılandırma yönetimi denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1232.json) |
|[Microsoft yönetilen denetim 1233-yapılandırma yönetimi planı](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9d79001f-95fe-45d0-8736-f217e78c1f57) |Microsoft bu yapılandırma yönetimi denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1233.json) |

### <a name="software-usage-restrictions"></a>Yazılım kullanım kısıtlamaları

**Kimlik**: nıst SP 800-53 R4 cm-10 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1234-yazılım kullanım kısıtlamaları](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb293f881-361c-47ed-b997-bc4e2296bc0b) |Microsoft bu yapılandırma yönetimi denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1234.json) |
|[Microsoft yönetilen denetim 1235-yazılım kullanım kısıtlamaları](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc49c610b-ece4-44b3-988c-2172b70d6e46) |Microsoft bu yapılandırma yönetimi denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1235.json) |
|[Microsoft yönetilen denetim 1236-yazılım kullanım kısıtlamaları](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9ba3ed84-c768-4e18-b87c-34ef1aff1b57) |Microsoft bu yapılandırma yönetimi denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1236.json) |

### <a name="software-usage-restrictions--open-source-software"></a>Yazılım kullanım kısıtlamaları | Açık kaynak yazılım

**Kimlik**: nıst SP 800-53 R4 cm-10 (1) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1237-yazılım kullanım kısıtlamaları \| açık kaynak yazılım](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe80b6812-0bfa-4383-8223-cdd86a46a890) |Microsoft bu yapılandırma yönetimi denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1237.json) |

### <a name="user-installed-software"></a>User-Installed yazılım

**Kimlik**: nıst SP 800-53 R4 cm-11 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Güvenli uygulamaları tanımlamaya yönelik Uyarlamalı uygulama denetimleri, makinelerinizde etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |Makinelerinizde çalışan bilinen güvenli uygulamaların listesini tanımlamak için uygulama denetimlerini etkinleştirin ve diğer uygulamalar çalıştırıldığında sizi uyarır. Bu, makinelerinizi kötü amaçlı yazılımlara karşı korumanıza yardımcı olur. Güvenlik Merkezi, kurallarınızı yapılandırma ve sürdürme sürecini basitleştirmek için makine öğrenimini kullanarak her makinede çalışan uygulamaları analiz eder ve bilinen güvenli uygulamalar listesini önerir. |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |
|[Microsoft yönetilen denetim 1238-User-Installed yazılım](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa36cedd4-3ffd-4b1f-8b18-aa71d8d87ce1) |Microsoft bu yapılandırma yönetimi denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1238.json) |
|[Microsoft yönetilen denetim 1239-User-Installed yazılım](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0be51298-f643-4556-88af-d7db90794879) |Microsoft bu yapılandırma yönetimi denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1239.json) |
|[Microsoft yönetilen denetim 1240-User-Installed yazılım](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F129eb39f-d79a-4503-84cd-92f036b5e429) |Microsoft bu yapılandırma yönetimi denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1240.json) |

### <a name="user-installed-software--alerts-for-unauthorized-installations"></a>User-Installed yazılım | Yetkisiz yüklemeler için uyarılar

**Kimlik**: nıst SP 800-53 R4 cm-11 (1) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1241- \| yetkisiz yüklemeler için User-Installed yazılım uyarıları](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feca4d7b2-65e2-4e04-95d4-c68606b063c3) |Microsoft bu yapılandırma yönetimi denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1241.json) |

## <a name="contingency-planning"></a>Yedek planlama

### <a name="contingency-planning-policy-and-procedures"></a>Yedek planlama Ilkesi ve yordamları

**Kimlik**: nıst SP 800-53 R4 CP-1 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1242-yedek planlama Ilkesi ve yordamları](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf3b3293-667a-445e-a722-fa0b0afc0958) |Microsoft bu yedek planlama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1242.json) |
|[Microsoft yönetilen denetim 1243-yedek planlama Ilkesi ve yordamları](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fca9a4469-d6df-4ab2-a42f-1213c396f0ec) |Microsoft bu yedek planlama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1243.json) |

### <a name="contingency-plan"></a>Yedek plan

**Kimlik**: nıst SP 800-53 R4 CP-2 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetimi 1244-acil durum planı](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6a13a8f8-c163-4b1b-8554-d63569dab937) |Microsoft bu yedek planlama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1244.json) |
|[Microsoft yönetilen denetimi 1245-acil durum planı](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa0e45314-57b8-4623-80cd-bbb561f59516) |Microsoft bu yedek planlama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1245.json) |
|[Microsoft yönetilen denetimi 1246-acil durum planı](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F398eb61e-8111-40d5-a0c9-003df28f1753) |Microsoft bu yedek planlama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1246.json) |
|[Microsoft yönetilen denetimi 1247-acil durum planı](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4e666db5-b2ef-4b06-aac6-09bfce49151b) |Microsoft bu yedek planlama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1247.json) |
|[Microsoft yönetilen denetimi 1248-acil durum planı](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F50fc602d-d8e0-444b-a039-ad138ee5deb0) |Microsoft bu yedek planlama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1248.json) |
|[Microsoft yönetilen denetimi 1249-acil durum planı](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd3bf4251-0818-42db-950b-afd5b25a51c2) |Microsoft bu yedek planlama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1249.json) |
|[Microsoft yönetilen denetimi 1250-acil durum planı](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8de614d8-a8b7-4f70-a62a-6d37089a002c) |Microsoft bu yedek planlama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1250.json) |

### <a name="contingency-plan--coordinate-with-related-plans"></a>Acil durum planı | Ilgili planlarla koordine et

**Kimlik**: nıst SP 800-53 R4 CP-2 (1) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1251- \| Ilgili planlarla yedek plan koordinatı](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5e2b3730-8c14-4081-8893-19dbb5de7348) |Microsoft bu yedek planlama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1251.json) |

### <a name="contingency-plan--capacity-planning"></a>Acil durum planı | Kapasite planlama

**Kimlik**: nıst SP 800-53 R4 CP-2 (2) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1252-yedek plan \| Kapasite planlaması](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa328fd72-8ff5-4f96-8c9c-b30ed95db4ab) |Microsoft bu yedek planlama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1252.json) |

### <a name="contingency-plan--resume-essential-missions--business-functions"></a>Acil durum planı | Önemli görevler/Iş Işlevlerini sürdürür

**Kimlik**: nıst SP 800-53 R4 CP-2 (3) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetimi 1253-acil durum planı \| önemli görevler/Iş işlevlerini sürdürür](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0afce0b3-dd9f-42bb-af28-1e4284ba8311) |Microsoft bu yedek planlama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1253.json) |

### <a name="contingency-plan--resume-all-missions--business-functions"></a>Acil durum planı | Tüm görevler/Iş Işlevlerini sürdürür

**Kimlik**: nıst SP 800-53 R4 CP-2 (4) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetimi 1254-acil durum planı \| Tüm görevler/Iş işlevlerini sürdürür](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F704e136a-4fe0-427c-b829-cd69957f5d2b) |Microsoft bu yedek planlama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1254.json) |

### <a name="contingency-plan--continue--essential-missions--business-functions"></a>Acil durum planı | Önemli görevler/Iş Işlevlerine devam et

**Kimlik**: nıst SP 800-53 R4 CP-2 (5) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1255-acil durum planı \| devam et önemli görevler/Iş işlevleri](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff3793f5e-937f-44f7-bfba-40647ef3efa0) |Microsoft bu yedek planlama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1255.json) |

### <a name="contingency-plan--identify-critical-assets"></a>Acil durum planı | Kritik varlıkları tanımla

**Kimlik**: nıst SP 800-53 R4 CP-2 (8) **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1256- \| kritik varlıkları tanımlamak Için yedek plan](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F232ab24b-810b-4640-9019-74a7d0d6a980) |Microsoft bu yedek planlama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1256.json) |

### <a name="contingency-training"></a>Acil durum eğitimi

**Kimlik**: nıst SP 800-53 R4 CP-3 **sahipliği**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetimi 1257-acil durum eğitimi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb958b241-4245-4bd6-bd2d-b8f0779fb543) |Microsoft bu yedek planlama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1257.json) |
|[Microsoft yönetilen denetimi 1258-acil durum eğitimi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7814506c-382c-4d33-a142-249dd4a0dbff) |Microsoft bu yedek planlama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1258.json) |
|[Microsoft yönetilen denetimi 1259-acil durum eğitimi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9d9e18f7-bad9-4d30-8806-a0c9d5e26208) |Microsoft bu yedek planlama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1259.json) |

### <a name="contingency-training--simulated-events"></a>Acil durum eğitimi | Benzetimli olaylar

**Kimlik**: nıst SP 800-53 R4 CP-3 (1) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1260-acil durum eğitimi \| benzetimi etkinlikleri](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F42254fc4-2738-4128-9613-72aaa4f0d9c3) |Microsoft bu yedek planlama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1260.json) |

### <a name="contingency-plan-testing"></a>Yedek plan testi

**Kimlik**: nıst SP 800-53 R4 CP-4 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1261-yedek plan testi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F65aeceb5-a59c-4cb1-8d82-9c474be5d431) |Microsoft bu yedek planlama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1261.json) |
|[Microsoft yönetilen denetim 1262-yedek plan testi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F831e510e-db41-4c72-888e-a0621ab62265) |Microsoft bu yedek planlama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1262.json) |
|[Microsoft yönetilen denetim 1263-yedek plan testi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F41472613-3b05-49f6-8fe8-525af113ce17) |Microsoft bu yedek planlama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1263.json) |

### <a name="contingency-plan-testing--coordinate-with-related-plans"></a>Yedek plan testi | Ilgili planlarla koordine et

**Kimlik**: nıst SP 800-53 R4 CP-4 (1) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1264- \| Ilgili planlarla, ön yedek planı test koordinatı](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdd280d4b-50a1-42fb-a479-ece5878acf19) |Microsoft bu yedek planlama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1264.json) |

### <a name="contingency-plan-testing--alternate-processing-site"></a>Yedek plan testi | Alternatif Işleme sitesi

**Kimlik**: nıst SP 800-53 R4 CP-4 (2) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1265-yedek plan testi \| Alternatif Işleme sitesi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa18adb5b-1db6-4a5b-901a-7d3797d12972) |Microsoft bu yedek planlama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1265.json) |
|[Microsoft yönetilen denetim 1266-yedek plan testi \| Alternatif Işleme sitesi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3b4a3eb2-c25d-40bf-ad41-5094b6f59cee) |Microsoft bu yedek planlama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1266.json) |

### <a name="alternate-storage-site"></a>Alternatif depolama sitesi

**Kimlik**: nıst SP 800-53 R4 CP-6 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1267-alternatif depolama sitesi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4e97ba1d-be5d-4953-8da4-0cccf28f4805) |Microsoft bu yedek planlama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1267.json) |
|[Microsoft yönetilen denetim 1268-alternatif depolama sitesi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F23f6e984-3053-4dfc-ab48-543b764781f5) |Microsoft bu yedek planlama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1268.json) |

### <a name="alternate-storage-site--separation-from-primary-site"></a>Alternatif depolama sitesi | Birincil siteden ayırma

**Kimlik**: nıst SP 800-53 R4 CP-6 (1) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1269-birincil siteden diğer depolama site \| ayrımı](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F19b9439d-865d-4474-b17d-97d2702fdb66) |Microsoft bu yedek planlama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1269.json) |

### <a name="alternate-storage-site--recovery-time--point-objectives"></a>Alternatif depolama sitesi | Kurtarma zamanı/nokta hedefleri

**Kimlik**: nıst SP 800-53 R4 CP-6 (2) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1270-alternatif depolama site \| kurtarma zamanı/nokta hedefleri](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F53c76a39-2097-408a-b237-b279f7b4614d) |Microsoft bu yedek planlama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1270.json) |

### <a name="alternate-storage-site--accessibility"></a>Alternatif depolama sitesi | Larınızdaki

**Kimlik**: nıst SP 800-53 R4 CP-6 (3) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1271-alternatif depolama sitesi \| erişilebilirliği](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fda3bfb53-9c46-4010-b3db-a7ba1296dada) |Microsoft bu yedek planlama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1271.json) |

### <a name="alternate-processing-site"></a>Alternatif Işleme sitesi

**Kimlik**: nıst SP 800-53 R4 CP-7 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Olağanüstü durum kurtarma yapılandırması olmadan sanal makineleri denetleme](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0015ea4d-51ff-4ce3-8d8c-f3f8f0179a56) |Olağanüstü durum kurtarma yapılandırması olmayan sanal makineleri denetleyin. Olağanüstü durum kurtarma hakkında daha fazla bilgi edinmek için adresini ziyaret edin [https://aka.ms/asr-doc](https://aka.ms/asr-doc) . |Auditınotexists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/RecoveryServices_DisasterRecovery_Audit.json) |
|[Microsoft yönetilen denetim 1272-alternatif Işleme sitesi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fae46cf7a-e3fd-427b-9b91-44bc78e2d9d8) |Microsoft bu yedek planlama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1272.json) |
|[Microsoft yönetilen denetim 1273-alternatif Işleme sitesi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe77fcbf2-a1e8-44f1-860e-ed6583761e65) |Microsoft bu yedek planlama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1273.json) |
|[Microsoft yönetilen denetim 1274-alternatif Işleme sitesi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2aee175f-cd16-4825-939a-a85349d96210) |Microsoft bu yedek planlama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1274.json) |

### <a name="alternate-processing-site--separation-from-primary-site"></a>Alternatif Işleme sitesi | Birincil siteden ayırma

**Kimlik**: nıst SP 800-53 R4 CP-7 (1) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1275-birincil siteden diğer Işleme site \| ayrımı](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa23d9d53-ad2e-45ef-afd5-e6d10900a737) |Microsoft bu yedek planlama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1275.json) |

### <a name="alternate-processing-site--accessibility"></a>Alternatif Işleme sitesi | Larınızdaki

**Kimlik**: nıst SP 800-53 R4 CP-7 (2) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1276-alternatif Işleme site \| erişilebilirliği](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe214e563-1206-4a43-a56b-ac5880c9c571) |Microsoft bu yedek planlama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1276.json) |

### <a name="alternate-processing-site--priority-of-service"></a>Alternatif Işleme sitesi | Hizmet önceliği

**Kimlik**: nıst SP 800-53 R4 CP-7 (3) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1277-hizmetin farklı Işleme sitesi \| önceliği](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdc43e829-3d50-4a0a-aa0f-428d551862aa) |Microsoft bu yedek planlama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1277.json) |

### <a name="alternate-processing-site--preparation-for-use"></a>Alternatif Işleme sitesi | Kullanıma hazırlık

**Kimlik**: nıst SP 800-53 R4 CP-7 (4) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1278-Kullanım Için alternatif Işleme site \| hazırlığı](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8e5ef485-9e16-4c53-a475-fbb8107eac59) |Microsoft bu yedek planlama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1278.json) |

### <a name="telecommunications-services"></a>Telekomünikasyon hizmetleri

**Kimlik**: nıst SP 800-53 R4 CP-8 **sahipliği**: Microsoft

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1279-telekomünikasyon hizmetleri](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7d00bcd6-963d-4c02-ad8e-b45fa50bf3b0) |Microsoft bu yedek planlama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1279.json) |

### <a name="telecommunications-services--priority-of-service-provisions"></a>Telekomünikasyon hizmetleri | Hizmet provizyonlarından oluşan öncelik

**Kimlik**: nıst SP 800-53 R4 CP-8 (1) **sahiplik**: Microsoft

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetimi 1280- \| hizmet provizyonlarından telekomünikasyon hizmetleri önceliği](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffa108498-b3a8-4ffb-9e79-1107e76afad3) |Microsoft bu yedek planlama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1280.json) |
|[Microsoft yönetilen denetimi 1281- \| hizmet provizyonlarından telekomünikasyon hizmetleri önceliği](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8dc459b3-0e77-45af-8d71-cfd8c9654fe2) |Microsoft bu yedek planlama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1281.json) |

### <a name="telecommunications-services--single-points-of-failure"></a>Telekomünikasyon hizmetleri | Tek hata noktaları

**Kimlik**: nıst SP 800-53 R4 CP-8 (2) **sahiplik**: Microsoft

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1282-telekomünikasyon hizmetleri \| tek hata noktaları](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34042a97-ec6d-4263-93d2-8c1c46823b2a) |Microsoft bu yedek planlama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1282.json) |

### <a name="telecommunications-services--separation-of-primary--alternate-providers"></a>Telekomünikasyon hizmetleri | Birincil/alternatif sağlayıcıların ayrımı

**Kimlik**: nıst SP 800-53 R4 CP-8 (3) **sahiplik**: Microsoft

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1283- \| birincil/alternatif sağlayıcıların telekomünikasyon hizmetleri ayrımı](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa9172e76-7f56-46e9-93bf-75d69bdb5491) |Microsoft bu yedek planlama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1283.json) |

### <a name="telecommunications-services--provider-contingency-plan"></a>Telekomünikasyon hizmetleri | Sağlayıcı yedek planı

**Kimlik**: nıst SP 800-53 R4 CP-8 (4) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1284-telekomünikasyon hizmetleri \| sağlayıcısı yedek planı](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F942b3e97-6ae3-410e-a794-c9c999b97c0b) |Microsoft bu yedek planlama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1284.json) |
|[Microsoft yönetilen denetim 1285-telekomünikasyon hizmetleri \| sağlayıcısı yedek planı](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F01f7726b-db54-45c2-bcb5-9bd7a43796ee) |Microsoft bu yedek planlama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1285.json) |
|[Microsoft yönetilen denetim 1286-telekomünikasyon hizmetleri \| sağlayıcısı yedek planı](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4f9b47a-2116-4e6f-88db-4edbf22753f1) |Microsoft bu yedek planlama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1286.json) |

### <a name="information-system-backup"></a>Bilgi sistemi yedeklemesi

**Kimlik**: nıst SP 800-53 R4 CP-9 **sahipliği**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1287-bilgi sistemi yedeklemesi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F819dc6da-289d-476e-8500-7e341ef8677d) |Microsoft bu yedek planlama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1287.json) |
|[Microsoft yönetilen denetim 1288-bilgi sistemi yedeklemesi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8d854c3b-a3e6-4ec9-9f0c-c7274dbaeb2f) |Microsoft bu yedek planlama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1288.json) |
|[Microsoft yönetilen denetim 1289-bilgi sistemi yedeklemesi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7a724864-956a-496c-b778-637cb1d762cf) |Microsoft bu yedek planlama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1289.json) |
|[Microsoft yönetilen denetim 1290-bilgi sistemi yedeklemesi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F92f85ce9-17b7-49ea-85ee-ea7271ea6b82) |Microsoft bu yedek planlama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1290.json) |

### <a name="information-system-backup--testing-for-reliability--integrity"></a>Bilgi sistemi yedeklemesi | Güvenilirlik/bütünlük için test etme

**Kimlik**: nıst SP 800-53 R4 CP-9 (1) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1291- \| güvenilirlik/bütünlük Için bilgi sistemi yedekleme testi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d8fd073-9c85-4ee2-a9d0-2e4ec9eb8912) |Microsoft bu yedek planlama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1291.json) |

### <a name="information-system-backup--test-restoration-using-sampling"></a>Bilgi sistemi yedeklemesi | Örnekleme kullanarak geri yüklemeyi test etme

**Kimlik**: nıst SP 800-53 R4 CP-9 (2) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1292-örnekleme kullanılarak bilgi sistemi yedekleme \| testi geri yüklemesi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd03516cf-0293-489f-9b32-a18f2a79f836) |Microsoft bu yedek planlama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1292.json) |

### <a name="information-system-backup--separate-storage-for-critical-information"></a>Bilgi sistemi yedeklemesi | Kritik bilgiler için depolama alanı ayırma

**Kimlik**: nıst SP 800-53 R4 CP-9 (3) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1293- \| önemli bilgiler Için bilgi sistemi yedeklemesi ayrı depolama](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F87f7cd82-2e45-4d0f-9e2f-586b0962d142) |Microsoft bu yedek planlama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1293.json) |

### <a name="information-system-backup--transfer-to-alternate-storage-site"></a>Bilgi sistemi yedeklemesi | Alternatif depolama sitesine aktar

**Kimlik**: nıst SP 800-53 R4 CP-9 (5) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1294- \| alternatif depolama sitesine bilgi sistemi yedekleme aktarımı](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F49dbe627-2c1e-438c-979e-dd7a39bbf81d) |Microsoft bu yedek planlama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1294.json) |

### <a name="information-system-recovery-and-reconstitution"></a>Bilgi sistemi kurtarma ve Reconstitution

**Kimlik**: nıst SP 800-53 R4 CP-10 **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1295-bilgi sistemi kurtarma ve Reconstitution](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa895fbdb-204d-4302-9689-0a59dc42b3d9) |Microsoft bu yedek planlama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1295.json) |

### <a name="information-system-recovery-and-reconstitution--transaction-recovery"></a>Bilgi sistem kurtarma ve Reconstitution | İşlem kurtarma

**Kimlik**: nıst SP 800-53 R4 CP-10 (2) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1296-bilgi sistemi kurtarma ve Reconstitution \| Işlem kurtarma](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe57b98a0-a011-4956-a79d-5d17ed8b8e48) |Microsoft bu yedek planlama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1296.json) |

### <a name="information-system-recovery-and-reconstitution--restore-within-time-period"></a>Bilgi sistem kurtarma ve Reconstitution | Zaman dönemi Içinde geri yükleme

**Kimlik**: nıst SP 800-53 R4 CP-10 (4) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1297-zaman dönemi Içinde bilgi sistemi kurtarma ve Reconstitution \| geri yükleme](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F93fd8af1-c161-4bae-9ba9-f62731f76439) |Microsoft bu yedek planlama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1297.json) |

## <a name="identification-and-authentication"></a>Tanımlama ve kimlik doğrulama

### <a name="identification-and-authentication-policy-and-procedures"></a>Tanımlama ve kimlik doğrulama Ilkesi ve yordamları

**Kimlik**: nıst SP 800-53 R4 IA-1 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1298-tanımlama ve kimlik doğrulama Ilkesi ve yordamları](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1dc784b5-4895-4d27-9d40-a06b032bd1ee) |Microsoft bu tanımlama ve kimlik doğrulama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1298.json) |
|[Microsoft yönetilen denetim 1299-tanımlama ve kimlik doğrulama Ilkesi ve yordamları](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffd4e54f7-9ab0-4bae-b6cc-457809948a89) |Microsoft bu tanımlama ve kimlik doğrulama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1299.json) |

### <a name="identification-and-authentication-organizational-users"></a>Tanımlama ve kimlik doğrulama (Kurumsal kullanıcılar)

**Kimlik**: nıst SP 800-53 R4 IA-2 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1300-tanımlama ve kimlik doğrulaması (Kurumsal kullanıcılar)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F99deec7d-5526-472e-b07c-3645a792026a) |Microsoft bu tanımlama ve kimlik doğrulama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1300.json) |

### <a name="identification-and-authentication-organizational-users--network-access-to-privileged-accounts"></a>Tanımlama ve kimlik doğrulaması (Kurumsal kullanıcılar) | Ayrıcalıklı hesaplara ağ erişimi

**Kimlik**: nıst SP 800-53 R4 IA-2 (1) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Aboneliğinizde yazma izinleri olan hesaplarda MFA etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |Hesapların veya kaynakların ihlal oluşmasını engellemek için yazma ayrıcalıklarına sahip tüm abonelik hesapları için Multi-Factor Authentication (MFA) etkinleştirilmelidir. |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |
|[MFA, aboneliğinizde sahip izinleri olan hesaplarda etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |Multi-Factor Authentication (MFA), hesapların veya kaynakların ihlaline engel olmak için sahip izinleri olan tüm abonelik hesapları için etkinleştirilmelidir. |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |
|[Microsoft yönetilen denetim 1301-tanımlayıcı ve kimlik doğrulaması (org. Users) \| ayrıcalıklı hesaplara ağ erişimi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6a8e0cc-ac23-468b-abe4-a8a1cc6d7a08) |Microsoft bu tanımlama ve kimlik doğrulama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1301.json) |

### <a name="identification-and-authentication-organizational-users--network-access-to-non-privileged-accounts"></a>Tanımlama ve kimlik doğrulaması (Kurumsal kullanıcılar) | Ayrıcalıklı olmayan hesaplara ağ erişimi

**Kimlik**: nıst SP 800-53 R4 IA-2 (2) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[MFA, aboneliğinizde okuma izinleri olan hesaplarda etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |Hesapların veya kaynakların ihlal oluşmasını engellemek için okuma ayrıcalıklarına sahip tüm abonelik hesapları için Multi-Factor Authentication (MFA) etkinleştirilmelidir. |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |
|[Microsoft yönetilen denetim 1302-kimliği ve kimlik doğrulaması (kuruluş. kullanıcılar) \| ayrıcalıklı olmayan hesaplara ağ erişimi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09828c65-e323-422b-9774-9d5c646124da) |Microsoft bu tanımlama ve kimlik doğrulama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1302.json) |

### <a name="identification-and-authentication-organizational-users--local-access-to-privileged-accounts"></a>Tanımlama ve kimlik doğrulaması (Kurumsal kullanıcılar) | Ayrıcalıklı hesaplara yerel erişim

**Kimlik**: nıst SP 800-53 R4 IA-2 (3) **sahipliği**: Microsoft

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1303-tanımlayıcı ve kimlik doğrulaması (org. Users) \| ayrıcalıklı hesaplara yerel erişim](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F80ca0a27-918a-4604-af9e-723a27ee51e8) |Microsoft bu tanımlama ve kimlik doğrulama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1303.json) |

### <a name="identification-and-authentication-organizational-users--local-access-to-non-privileged-accounts"></a>Tanımlama ve kimlik doğrulaması (Kurumsal kullanıcılar) | Ayrıcalıklı olmayan hesaplara yerel erişim

**Kimlik**: nıst SP 800-53 R4 IA-2 (4) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1304-tanımlayıcı ve kimlik doğrulaması (org. Users) \| ayrıcalıklı olmayan hesaplara yerel erişim](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6ca71be3-16cb-4d39-8b50-7f8fd5e2f11b) |Microsoft bu tanımlama ve kimlik doğrulama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1304.json) |

### <a name="identification-and-authentication-organizational-users--group-authentication"></a>Tanımlama ve kimlik doğrulaması (Kurumsal kullanıcılar) | Grup kimlik doğrulaması

**Kimlik**: nıst SP 800-53 R4 IA-2 (5) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1305-tanımlama ve kimlik doğrulaması (org. Users) \| grubu kimlik doğrulaması](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9d9166a8-1722-4b8f-847c-2cf3f2618b3d) |Microsoft bu tanımlama ve kimlik doğrulama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1305.json) |

### <a name="identification-and-authentication-organizational-users--network-access-to-privileged-accounts---replay-resistant"></a>Tanımlama ve kimlik doğrulaması (Kurumsal kullanıcılar) | Ayrıcalıklı hesaplara ağ erişimi-dayanıklı bir şekilde yeniden yürütün

**Kimlik**: nıst SP 800-53 R4 IA-2 (8) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1306-tanımlama ve kimlik doğrulaması (org. Users) \| net. PRIV. Accts 'ye erişim.-yeniden Yürüt](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcafc6c3c-5fc5-4c5e-a99b-a0ccb1d34eff) |Microsoft bu tanımlama ve kimlik doğrulama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1306.json) |

### <a name="identification-and-authentication-organizational-users--network-access-to-non-privileged-accounts---replay-resistant"></a>Tanımlama ve kimlik doğrulaması (Kurumsal kullanıcılar) | Ayrıcalıklı olmayan hesaplara ağ erişimi-dayanıklı bir şekilde yeniden yürütün

**Kimlik**: nıst SP 800-53 R4 IA-2 (9) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1307-tanımlama ve kimlik doğrulaması (org. Users) \| net. PRIV olmayan erişim. Accts.-Replay](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F84e622c8-4bed-417c-84c6-b2fb0dd73682) |Microsoft bu tanımlama ve kimlik doğrulama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1307.json) |

### <a name="identification-and-authentication-organizational-users--remote-access----separate-device"></a>Tanımlama ve kimlik doğrulaması (Kurumsal kullanıcılar) | Uzaktan erişim-ayrı cihaz

**Kimlik**: nıst SP 800-53 R4 IA-2 (11) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1308-tanımlama ve kimlik doğrulaması (org. Users) \| Uzaktan erişim-ayrı cihaz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F81817e1c-5347-48dd-965a-40159d008229) |Microsoft bu tanımlama ve kimlik doğrulama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1308.json) |

### <a name="identification-and-authentication-organizational-users--acceptance-of-piv-credentials"></a>Tanımlama ve kimlik doğrulaması (Kurumsal kullanıcılar) | PIV kimlik bilgilerini kabul etme

**Kimlik**: nıst SP 800-53 R4 IA-2 (12) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1309-tanımlama ve kimlik doğrulaması (org. Users) \| PIV kimlik bilgilerini kabul etme](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff355d62b-39a8-4ba3-abf7-90f71cb3b000) |Microsoft bu tanımlama ve kimlik doğrulama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1309.json) |

### <a name="device-identification-and-authentication"></a>Cihaz kimliği ve kimlik doğrulaması

**Kimlik**: nıst SP 800-53 R4 IA-3 **sahipliği**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1310-cihaz tanımlama ve kimlik doğrulaması](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F450d7ede-823d-4931-a99d-57f6a38807dc) |Microsoft bu tanımlama ve kimlik doğrulama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1310.json) |

### <a name="identifier-management"></a>Tanımlayıcı yönetimi

**Kimlik**: nıst SP 800-53 R4 IA-4 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetimi 1311-tanımlayıcı yönetimi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe7568697-0c9e-4ea3-9cec-9e567d14f3c6) |Microsoft bu tanımlama ve kimlik doğrulama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1311.json) |
|[Microsoft yönetilen denetimi 1312-tanımlayıcı yönetimi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4d6a5968-9eef-4c18-8534-376790ab7274) |Microsoft bu tanımlama ve kimlik doğrulama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1312.json) |
|[Microsoft yönetilen denetimi 1313-tanımlayıcı yönetimi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F36220f5b-79a1-4cdb-8c74-2d2449f9a510) |Microsoft bu tanımlama ve kimlik doğrulama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1313.json) |
|[Microsoft yönetilen denetimi 1314-tanımlayıcı yönetimi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef0c8530-efd9-45b8-b753-f03083d06295) |Microsoft bu tanımlama ve kimlik doğrulama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1314.json) |
|[Microsoft yönetilen denetimi 1315-tanımlayıcı yönetimi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3aa87116-f1a1-4edb-bfbf-14e036f8d454) |Microsoft bu tanımlama ve kimlik doğrulama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1315.json) |

### <a name="identifier-management--identify-user-status"></a>Tanımlayıcı yönetimi | Kullanıcı durumunu tanımla

**Kimlik**: nıst SP 800-53 R4 IA-4 (4) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1316-tanımlayıcı Yönetimi \| Kullanıcı durumunu tanımlama](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8ce14753-66e5-465d-9841-26ef55c09c0d) |Microsoft bu tanımlama ve kimlik doğrulama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1316.json) |

### <a name="authenticator-management"></a>Doğrulayıcı yönetimi

**Kimlik**: nıst SP 800-53 R4 IA-5 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Kimliği olmayan sanal makinelerde Konuk yapılandırma atamalarını etkinleştirmek için sistem tarafından atanan yönetilen kimlik Ekle](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3cf2ab00-13f1-4d0c-8971-2ac904541a7e) |Bu ilke, Azure 'da barındırılan, Konuk yapılandırması tarafından desteklenen ancak herhangi bir yönetilen kimliği bulunmayan sanal makinelere sistem tarafından atanan yönetilen bir kimlik ekler. Sistem tarafından atanan yönetilen kimlik, tüm konuk yapılandırma atamaları için bir önkoşuldur ve herhangi bir konuk yapılandırma ilkesi tanımı kullanılmadan önce makinelere eklenmelidir. Konuk yapılandırması hakkında daha fazla bilgi için, adresini ziyaret edin [https://aka.ms/gcpol](https://aka.ms/gcpol) . |değiştir |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AddSystemIdentityWhenNone_Prerequisite.json) |
|[Kullanıcı tarafından atanan bir kimliğe sahip VM 'lerde Konuk yapılandırma atamalarını etkinleştirmek için sistem tarafından atanan yönetilen kimlik Ekle](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F497dff13-db2a-4c0f-8603-28fa3b331ab6) |Bu ilke, Azure 'da barındırılan sanal makinelere, Konuk yapılandırması tarafından desteklenen ve en az bir kullanıcı tarafından atanan kimliğe sahip ancak sistem tarafından atanan bir yönetilen kimliğe sahip olmayan bir yönetilen kimlik ekler. Sistem tarafından atanan yönetilen kimlik, tüm konuk yapılandırma atamaları için bir önkoşuldur ve herhangi bir konuk yapılandırma ilkesi tanımı kullanılmadan önce makinelere eklenmelidir. Konuk yapılandırması hakkında daha fazla bilgi için, adresini ziyaret edin [https://aka.ms/gcpol](https://aka.ms/gcpol) . |değiştir |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AddSystemIdentityWhenUser_Prerequisite.json) |
|[Passwd dosyası izinleri 0644 olarak ayarlanan Linux makinelerini denetleme](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe6955644-301c-44b5-a4c4-528577de6861) |Önkoşulların ilke atama kapsamına dağıtılmasını gerektirir. Ayrıntılar için, adresini ziyaret edin [https://aka.ms/gcpol](https://aka.ms/gcpol) . Passwd dosyası izinleri 0644 olarak ayarlanmayan Linux makinelerinde makineler uyumlu değil |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_LinuxPassword121_AINE.json) |
|[Parolası olmayan hesaplara sahip Linux makinelerini denetleme](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6ec09a3-78bf-4f8f-99dc-6c77182d0f99) |Önkoşulların ilke atama kapsamına dağıtılmasını gerektirir. Ayrıntılar için, adresini ziyaret edin [https://aka.ms/gcpol](https://aka.ms/gcpol) . Parolaları olmayan hesaplara sahip Linux makinelerinde makineler uyumlu değildir |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_LinuxPassword232_AINE.json) |
|[Parolaları, ters çevrilebilir şifreleme kullanarak depolamamayan Windows makinelerini denetleme](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fda0f98fe-a24b-4ad5-af69-bd0400233661) |Önkoşulların ilke atama kapsamına dağıtılmasını gerektirir. Ayrıntılar için, adresini ziyaret edin [https://aka.ms/gcpol](https://aka.ms/gcpol) . Ters çevrilebilir şifreleme kullanarak parolaları depolamayan Windows makineler ise, makineler uyumlu değildir |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsPasswordEncryption_AINE.json) |
|[Linux VM 'lerinde Konuk yapılandırma atamalarını etkinleştirmek için Linux konuk yapılandırma uzantısını dağıtma](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F331e8ea8-378a-410f-a2e5-ae22f38bb0da) |Bu ilke, Linux konuk yapılandırma uzantısını Azure 'da barındırılan ve konuk yapılandırması tarafından desteklenen Linux sanal makinelerine dağıtır. Linux konuk yapılandırma uzantısı, tüm Linux konuk yapılandırma atamaları için bir önkoşuldur ve Linux konuk yapılandırma ilkesi tanımı kullanılmadan önce makinelere dağıtılmalıdır. Konuk yapılandırması hakkında daha fazla bilgi için, adresini ziyaret edin [https://aka.ms/gcpol](https://aka.ms/gcpol) . |deployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_DeployExtensionLinux_Prerequisite.json) |
|[Windows VM 'lerde Konuk yapılandırma atamalarını etkinleştirmek için Windows Konuk yapılandırma uzantısı 'nı dağıtma](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F385f5831-96d4-41db-9a3c-cd3af78aaae6) |Bu ilke, Windows Konuk yapılandırma uzantısını Azure 'da barındırılan ve konuk yapılandırması tarafından desteklenen Windows sanal makinelerine dağıtır. Windows Konuk yapılandırma uzantısı, tüm Windows Konuk yapılandırma atamaları için bir önkoşuldur ve herhangi bir Windows Konuk yapılandırma ilkesi tanımı kullanılmadan önce makinelere dağıtılmalıdır. Konuk yapılandırması hakkında daha fazla bilgi için, adresini ziyaret edin [https://aka.ms/gcpol](https://aka.ms/gcpol) . |deployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_DeployExtensionWindows_Prerequisite.json) |
|[Microsoft yönetilen denetim 1317-Authenticator yönetimi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8877f519-c166-47b7-81b7-8a8eb4ff3775) |Microsoft bu tanımlama ve kimlik doğrulama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1317.json) |
|[Microsoft yönetilen denetim 1318-Authenticator yönetimi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffced5fda-3bdb-4d73-bfea-0e2c80428b66) |Microsoft bu tanımlama ve kimlik doğrulama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1318.json) |
|[Microsoft yönetilen denetim 1319-Authenticator yönetimi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F66f7ae57-5560-4fc5-85c9-659f204e7a42) |Microsoft bu tanımlama ve kimlik doğrulama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1319.json) |
|[Microsoft yönetilen denetim 1320-Authenticator yönetimi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6f54c732-71d4-4f93-a696-4e373eca3a77) |Microsoft bu tanımlama ve kimlik doğrulama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1320.json) |
|[Microsoft yönetilen denetim 1321-Authenticator yönetimi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feb627cc6-3a9d-46b5-96b7-5fca49178a37) |Microsoft bu tanımlama ve kimlik doğrulama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1321.json) |
|[Microsoft yönetilen denetim 1322-Authenticator yönetimi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9d1d971e-467e-4278-9633-c74c3d4fecc4) |Microsoft bu tanımlama ve kimlik doğrulama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1322.json) |
|[Microsoft yönetilen denetim 1323-Authenticator yönetimi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabe8f70b-680f-470c-9b86-a7edfb664ecc) |Microsoft bu tanımlama ve kimlik doğrulama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1323.json) |
|[Microsoft yönetilen denetim 1324-Authenticator yönetimi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cfea2b3-7f77-497e-ac20-0752f2ff6eee) |Microsoft bu tanımlama ve kimlik doğrulama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1324.json) |
|[Microsoft yönetilen denetim 1325-Authenticator yönetimi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1845796a-7581-49b2-ae20-443121538e19) |Microsoft bu tanımlama ve kimlik doğrulama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1325.json) |
|[Microsoft yönetilen denetim 1326-Authenticator yönetimi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8605fc00-1bf5-4fb3-984e-c95cec4f231d) |Microsoft bu tanımlama ve kimlik doğrulama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1326.json) |

### <a name="authenticator-management--password-based-authentication"></a>Doğrulayıcı yönetimi | Password-Based kimlik doğrulaması

**Kimlik**: nıst SP 800-53 R4 IA-5 (1) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Kimliği olmayan sanal makinelerde Konuk yapılandırma atamalarını etkinleştirmek için sistem tarafından atanan yönetilen kimlik Ekle](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3cf2ab00-13f1-4d0c-8971-2ac904541a7e) |Bu ilke, Azure 'da barındırılan, Konuk yapılandırması tarafından desteklenen ancak herhangi bir yönetilen kimliği bulunmayan sanal makinelere sistem tarafından atanan yönetilen bir kimlik ekler. Sistem tarafından atanan yönetilen kimlik, tüm konuk yapılandırma atamaları için bir önkoşuldur ve herhangi bir konuk yapılandırma ilkesi tanımı kullanılmadan önce makinelere eklenmelidir. Konuk yapılandırması hakkında daha fazla bilgi için, adresini ziyaret edin [https://aka.ms/gcpol](https://aka.ms/gcpol) . |değiştir |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AddSystemIdentityWhenNone_Prerequisite.json) |
|[Kullanıcı tarafından atanan bir kimliğe sahip VM 'lerde Konuk yapılandırma atamalarını etkinleştirmek için sistem tarafından atanan yönetilen kimlik Ekle](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F497dff13-db2a-4c0f-8603-28fa3b331ab6) |Bu ilke, Azure 'da barındırılan sanal makinelere, Konuk yapılandırması tarafından desteklenen ve en az bir kullanıcı tarafından atanan kimliğe sahip ancak sistem tarafından atanan bir yönetilen kimliğe sahip olmayan bir yönetilen kimlik ekler. Sistem tarafından atanan yönetilen kimlik, tüm konuk yapılandırma atamaları için bir önkoşuldur ve herhangi bir konuk yapılandırma ilkesi tanımı kullanılmadan önce makinelere eklenmelidir. Konuk yapılandırması hakkında daha fazla bilgi için, adresini ziyaret edin [https://aka.ms/gcpol](https://aka.ms/gcpol) . |değiştir |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AddSystemIdentityWhenUser_Prerequisite.json) |
|[Önceki 24 parolanın yeniden kullanılmasına izin veren Windows makinelerini denetleme](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b054a0d-39e2-4d53-bea3-9734cad2c69b) |Önkoşulların ilke atama kapsamına dağıtılmasını gerektirir. Ayrıntılar için, adresini ziyaret edin [https://aka.ms/gcpol](https://aka.ms/gcpol) . Windows makineleri önceki 24 parolanın yeniden kullanılmasına izin veren makineler uyumlu değildir |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsPasswordEnforce_AINE.json) |
|[Maksimum 70 gün parola yaşı olmayan Windows makinelerini denetle](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4ceb8dc2-559c-478b-a15b-733fbf1e3738) |Önkoşulların ilke atama kapsamına dağıtılmasını gerektirir. Ayrıntılar için, adresini ziyaret edin [https://aka.ms/gcpol](https://aka.ms/gcpol) . Maksimum parola yaşı 70 gün olmayan Windows makinelerinde makineler uyumlu değil |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsMaximumPassword_AINE.json) |
|[En az 1 günlük parola yaşı olmayan Windows makinelerini denetle](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F237b38db-ca4d-4259-9e47-7882441ca2c0) |Önkoşulların ilke atama kapsamına dağıtılmasını gerektirir. Ayrıntılar için, adresini ziyaret edin [https://aka.ms/gcpol](https://aka.ms/gcpol) . En az 1 günlük parola yaşı olmayan Windows makinelerinde makineler uyumlu değil |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsMinimumPassword_AINE.json) |
|[Parola karmaşıklığı ayarı etkin olmayan Windows makinelerini denetleme](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbf16e0bb-31e1-4646-8202-60a235cc7e74) |Önkoşulların ilke atama kapsamına dağıtılmasını gerektirir. Ayrıntılar için, adresini ziyaret edin [https://aka.ms/gcpol](https://aka.ms/gcpol) . Parola karmaşıklığı ayarı etkin olmayan Windows makinelerinde makineler uyumlu değil |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsPasswordComplexity_AINE.json) |
|[Minimum parola uzunluğunu 14 karakter olarak kısıtlayan Windows makinelerini denetle](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa2d0e922-65d0-40c4-8f87-ea6da2d307a2) |Önkoşulların ilke atama kapsamına dağıtılmasını gerektirir. Ayrıntılar için, adresini ziyaret edin [https://aka.ms/gcpol](https://aka.ms/gcpol) . En az parola uzunluğu 14 karakter olarak kısıtlanmayan Windows makineleri için makineler uyumlu değil |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsPasswordLength_AINE.json) |
|[Parolaları, ters çevrilebilir şifreleme kullanarak depolamamayan Windows makinelerini denetleme](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fda0f98fe-a24b-4ad5-af69-bd0400233661) |Önkoşulların ilke atama kapsamına dağıtılmasını gerektirir. Ayrıntılar için, adresini ziyaret edin [https://aka.ms/gcpol](https://aka.ms/gcpol) . Ters çevrilebilir şifreleme kullanarak parolaları depolamayan Windows makineler ise, makineler uyumlu değildir |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsPasswordEncryption_AINE.json) |
|[Linux VM 'lerinde Konuk yapılandırma atamalarını etkinleştirmek için Linux konuk yapılandırma uzantısını dağıtma](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F331e8ea8-378a-410f-a2e5-ae22f38bb0da) |Bu ilke, Linux konuk yapılandırma uzantısını Azure 'da barındırılan ve konuk yapılandırması tarafından desteklenen Linux sanal makinelerine dağıtır. Linux konuk yapılandırma uzantısı, tüm Linux konuk yapılandırma atamaları için bir önkoşuldur ve Linux konuk yapılandırma ilkesi tanımı kullanılmadan önce makinelere dağıtılmalıdır. Konuk yapılandırması hakkında daha fazla bilgi için, adresini ziyaret edin [https://aka.ms/gcpol](https://aka.ms/gcpol) . |deployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_DeployExtensionLinux_Prerequisite.json) |
|[Windows VM 'lerde Konuk yapılandırma atamalarını etkinleştirmek için Windows Konuk yapılandırma uzantısı 'nı dağıtma](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F385f5831-96d4-41db-9a3c-cd3af78aaae6) |Bu ilke, Windows Konuk yapılandırma uzantısını Azure 'da barındırılan ve konuk yapılandırması tarafından desteklenen Windows sanal makinelerine dağıtır. Windows Konuk yapılandırma uzantısı, tüm Windows Konuk yapılandırma atamaları için bir önkoşuldur ve herhangi bir Windows Konuk yapılandırma ilkesi tanımı kullanılmadan önce makinelere dağıtılmalıdır. Konuk yapılandırması hakkında daha fazla bilgi için, adresini ziyaret edin [https://aka.ms/gcpol](https://aka.ms/gcpol) . |deployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_DeployExtensionWindows_Prerequisite.json) |
|[Microsoft yönetilen denetim 1327-Authenticator Yönetimi \| Password-Based kimlik doğrulaması](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F03188d8f-1ae5-4fe1-974d-2d7d32ef937d) |Microsoft bu tanımlama ve kimlik doğrulama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1327.json) |
|[Microsoft yönetilen denetim 1328-Authenticator Yönetimi \| Password-Based kimlik doğrulaması](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff5c66fdc-3d02-4034-9db5-ba57802609de) |Microsoft bu tanımlama ve kimlik doğrulama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1328.json) |
|[Microsoft yönetilen denetim 1329-Authenticator Yönetimi \| Password-Based kimlik doğrulaması](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F498f6234-3e20-4b6a-a880-cbd646d973bd) |Microsoft bu tanımlama ve kimlik doğrulama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1329.json) |
|[Microsoft yönetilen denetim 1330-Authenticator Yönetimi \| Password-Based kimlik doğrulaması](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff75cedb2-5def-4b31-973e-b69e8c7bd031) |Microsoft bu tanımlama ve kimlik doğrulama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1330.json) |
|[Microsoft yönetilen denetim 1331-Authenticator Yönetimi \| Password-Based kimlik doğrulaması](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F05460fe2-301f-4ed1-8174-d62c8bb92ff4) |Microsoft bu tanımlama ve kimlik doğrulama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1331.json) |
|[Microsoft yönetilen denetim 1332-Authenticator Yönetimi \| Password-Based kimlik doğrulaması](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F068260be-a5e6-4b0a-a430-cd27071c226a) |Microsoft bu tanımlama ve kimlik doğrulama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1332.json) |

### <a name="authenticator-management--pki-based-authentication"></a>Doğrulayıcı yönetimi | Pki-Based kimlik doğrulaması

**Kimlik**: nıst SP 800-53 R4 IA-5 (2) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1333-Authenticator Yönetimi \| Pki-Based kimlik doğrulaması](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3298d6bf-4bc6-4278-a95d-f7ef3ac6e594) |Microsoft bu tanımlama ve kimlik doğrulama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1333.json) |
|[Microsoft yönetilen denetim 1334-Authenticator Yönetimi \| Pki-Based kimlik doğrulaması](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F44bfdadc-8c2e-4c30-9c99-f005986fabcd) |Microsoft bu tanımlama ve kimlik doğrulama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1334.json) |
|[Microsoft yönetilen denetim 1335-Authenticator Yönetimi \| Pki-Based kimlik doğrulaması](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F382016f3-d4ba-4e15-9716-55077ec4dc2a) |Microsoft bu tanımlama ve kimlik doğrulama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1335.json) |
|[Microsoft yönetilen denetim 1336-Authenticator Yönetimi \| Pki-Based kimlik doğrulaması](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F77f56280-e367-432a-a3b9-8ca2aa636a26) |Microsoft bu tanımlama ve kimlik doğrulama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1336.json) |

### <a name="authenticator-management--in-person-or-trusted-third-party-registration"></a>Doğrulayıcı yönetimi | yerinde veya güvenilir üçüncü taraf kaydı

**Kimlik**: nıst SP 800-53 R4 IA-5 (3) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1337-Authenticator Yönetimi \| In-Person veya güvenilir üçüncü taraf kaydı](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F463e5220-3f79-4e24-a63f-343e4096cd22) |Microsoft bu tanımlama ve kimlik doğrulama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1337.json) |

### <a name="authenticator-management--automated-support--for-password-strength-determination"></a>Doğrulayıcı yönetimi | Parola gücü belirleme otomatik desteği

**Kimlik**: nıst SP 800-53 R4 IA-5 (4) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1338-kimlik doğrulayıcı Yönetimi \| Parola gücü belirleme otomatik desteği](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6c59a207-6aed-41dc-83a2-e1ff66e4a4db) |Microsoft bu tanımlama ve kimlik doğrulama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1338.json) |

### <a name="authenticator-management--protection-of-authenticators"></a>Doğrulayıcı yönetimi | Kimlik doğrulayıcılar koruması

**Kimlik**: nıst SP 800-53 R4 IA-5 (6) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1339-kimlik doğrulayıcısının kimlik doğrulayıcı Yönetimi \| koruması](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F367ae386-db7f-4167-b672-984ff86277c0) |Microsoft bu tanımlama ve kimlik doğrulama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1339.json) |

### <a name="authenticator-management--no-embedded-unencrypted-static-authenticators"></a>Doğrulayıcı yönetimi | Gömülü şifrelenmemiş statik kimlik doğrulayıcılar yok

**Kimlik**: nıst SP 800-53 R4 IA-5 (7) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1340-Authenticator Yönetimi \| gömülü şifrelenmemiş statik kimlik doğrulayıcılar yok](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe51ff84b-e5ea-408f-b651-2ecc2933e4c6) |Microsoft bu tanımlama ve kimlik doğrulama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1340.json) |

### <a name="authenticator-management--multiple-information-system-accounts"></a>Doğrulayıcı yönetimi | Birden çok bilgi sistemi hesabı

**Kimlik**: nıst SP 800-53 R4 IA-5 (8) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1341-Authenticator Yönetimi \| birden çok bilgi sistemi hesabı](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34cb7e92-fe4c-4826-b51e-8cd203fa5d35) |Microsoft bu tanımlama ve kimlik doğrulama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1341.json) |

### <a name="authenticator-management--hardware-token-based-authentication"></a>Doğrulayıcı yönetimi | Donanım Token-Based kimlik doğrulaması

**Kimlik**: nıst SP 800-53 R4 IA-5 (11) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1342-Authenticator Yönetim \| donanımı Token-Based kimlik doğrulaması](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F283a4e29-69d5-4c94-b99e-29acf003c899) |Microsoft bu tanımlama ve kimlik doğrulama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1342.json) |

### <a name="authenticator-management--expiration-of-cached-authenticators"></a>Doğrulayıcı yönetimi | Önbelleğe alınan kimlik doğrulayıcılar süresi sonu

**Kimlik**: nıst SP 800-53 R4 IA-5 (13) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1343- \| önbelleğe alınan kimlik doğrulayıcı yönetimi süre sonu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c251a55-31eb-4e53-99c6-e9c43c393ac2) |Microsoft bu tanımlama ve kimlik doğrulama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1343.json) |

### <a name="authenticator-feedback"></a>Doğrulayıcı geri bildirimi

**Kimlik**: nıst SP 800-53 R4 IA-6 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1344-Authenticator geri bildirimi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c895fe7-2d8e-43a2-838c-3a533a5b355e) |Microsoft bu tanımlama ve kimlik doğrulama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1344.json) |

### <a name="cryptographic-module-authentication"></a>Şifreleme modülü kimlik doğrulaması

**Kimlik**: nıst SP 800-53 R4 IA-7 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1345-şifreleme modülü kimlik doğrulaması](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff86aa129-7c07-4aa4-bbf5-792d93ffd9ea) |Microsoft bu tanımlama ve kimlik doğrulama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1345.json) |

### <a name="identification-and-authentication-non-organizational-users"></a>Tanımlama ve kimlik doğrulaması (kurumsal olmayan kullanıcılar)

**Kimlik**: nıst SP 800-53 R4 IA-8 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1346-tanımlama ve kimlik doğrulaması (kurumsal olmayan kullanıcılar)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F464dc8ce-2200-4720-87a5-dc5952924cc6) |Microsoft bu tanımlama ve kimlik doğrulama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1346.json) |

### <a name="identification-and-authentication-non-organizational-users--acceptance-of-piv-credentials-from-other-agencies"></a>Tanımlama ve kimlik doğrulaması (kurumsal olmayan kullanıcılar) | Diğer kuruluşlardaki PIV kimlik bilgilerini kabul etme

**Kimlik**: nıst SP 800-53 R4 IA-8 (1) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1347-tanımlama ve kimlik doğrulaması (org dışı). Kullanıcılar) \| PIV kimlik bilgilerini kabul edin. Diğer Agys 'den.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F131a2706-61e9-4916-a164-00e052056462) |Microsoft bu tanımlama ve kimlik doğrulama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1347.json) |

### <a name="identification-and-authentication-non-organizational-users--acceptance-of-third-party-credentials"></a>Tanımlama ve kimlik doğrulaması (kurumsal olmayan kullanıcılar) | Üçüncü taraf kimlik bilgilerini kabul etme

**Kimlik**: nıst SP 800-53 R4 IA-8 (2) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1348-tanımlama ve kimlik doğrulaması (org dışı). Kullanıcılar) \| üçüncü taraf kimlik bilgilerini kabul etme](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F855ced56-417b-4d74-9d5f-dd1bc81e22d6) |Microsoft bu tanımlama ve kimlik doğrulama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1348.json) |

### <a name="identification-and-authentication-non-organizational-users--use-of-ficam-approved-products"></a>Tanımlama ve kimlik doğrulaması (kurumsal olmayan kullanıcılar) | Ficam-Approved ürünlerin kullanımı

**Kimlik**: nıst SP 800-53 R4 IA-8 (3) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1349-tanımlama ve kimlik doğrulaması (org dışı). Kullanıcılar) \| FICAM-Approved ürünlerin kullanımı](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17641f70-94cd-4a5d-a613-3d1143e20e34) |Microsoft bu tanımlama ve kimlik doğrulama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1349.json) |

### <a name="identification-and-authentication-non-organizational-users--use-of-ficam-issued-profiles"></a>Tanımlama ve kimlik doğrulaması (kurumsal olmayan kullanıcılar) | Ficam-Issued profillerinin kullanımı

**Kimlik**: nıst SP 800-53 R4 IA-8 (4) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1350-tanımlama ve kimlik doğrulaması (org dışı). Kullanıcılar) \| FICAM-Issued profillerinin kullanımı](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd77fd943-6ba6-4a21-ba07-22b03e347cc4) |Microsoft bu tanımlama ve kimlik doğrulama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1350.json) |

## <a name="incident-response"></a>Olay Yanıtı

### <a name="incident-response-policy-and-procedures"></a>Olay yanıtı Ilkesi ve yordamları

**Kimlik**: nıst SP 800-53 R4 IR-1 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1351-olay yanıtı Ilkesi ve yordamları](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbcfb6683-05e5-4ce6-9723-c3fbe9896bdd) |Microsoft bu olay yanıtı denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1351.json) |
|[Microsoft yönetilen denetim 1352-olay yanıtı Ilkesi ve yordamları](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F518cb545-bfa8-43f8-a108-3b7d5037469a) |Microsoft bu olay yanıtı denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1352.json) |

### <a name="incident-response-training"></a>Olay yanıtı eğitimi

**Kimlik**: nıst SP 800-53 R4 IR-2 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1353-olay yanıtı eğitimi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc785ad59-f78f-44ad-9a7f-d1202318c748) |Microsoft bu olay yanıtı denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1353.json) |
|[Microsoft yönetilen denetim 1354-olay yanıtı eğitimi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9fd92c17-163a-4511-bb96-bbb476449796) |Microsoft bu olay yanıtı denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1354.json) |
|[Microsoft yönetilen denetim 1355-olay yanıtı eğitimi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F90e01f69-3074-4de8-ade7-0fef3e7d83e0) |Microsoft bu olay yanıtı denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1355.json) |

### <a name="incident-response-training--simulated-events"></a>Olay yanıtı eğitimi | Benzetimli olaylar

**Kimlik**: nıst SP 800-53 R4 IR-2 (1) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1356-olay yanıt eğitimi \| benzetimi etkinlikleri](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8829f8f5-e8be-441e-85c9-85b72a5d0ef3) |Microsoft bu olay yanıtı denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1356.json) |

### <a name="incident-response-training--automated-training-environments"></a>Olay yanıtı eğitimi | Otomatikleştirilmiş eğitim ortamları

**Kimlik**: nıst SP 800-53 R4 IR-2 (2) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1357-olay yanıt eğitimi \| Otomatikleştirilmiş eğitim ortamları](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe4213689-05e8-4241-9d4e-8dd1cdafd105) |Microsoft bu olay yanıtı denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1357.json) |

### <a name="incident-response-testing"></a>Olay yanıtı testi

**Kimlik**: nıst SP 800-53 R4 IR-3 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1358-olay yanıtı testi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feffbaeef-5bf4-400d-895e-ef8cbc0e64c7) |Microsoft bu olay yanıtı denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1358.json) |

### <a name="incident-response-testing--coordination-with-related-plans"></a>Olay yanıtı testi | Ilgili planlarla koordinasyon

**Kimlik**: nıst SP 800-53 R4 IR-3 (2) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1359- \| Ilgili planlarla olay yanıtı testi düzenlemesi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47bc7ea0-7d13-4f7c-a154-b903f7194253) |Microsoft bu olay yanıtı denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1359.json) |

### <a name="incident-handling"></a>Olay Işleme

**Kimlik**: nıst SP 800-53 R4 IR-4 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1360-olay Işleme](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbe5b05e7-0b82-4ebc-9eda-25e447b1a41e) |Microsoft bu olay yanıtı denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1360.json) |
|[Microsoft yönetilen denetim 1361-olay Işleme](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F03ed3be1-7276-4452-9a5d-e4168565ac67) |Microsoft bu olay yanıtı denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1361.json) |
|[Microsoft yönetilen denetim 1362-olay Işleme](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5d169442-d6ef-439b-8dca-46c2c3248214) |Microsoft bu olay yanıtı denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1362.json) |

### <a name="incident-handling--automated-incident-handling-processes"></a>Olay Işleme | Otomatik olay Işleme Işlemleri

**Kimlik**: nıst SP 800-53 R4 IR-4 (1) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1363-olay işleme \| Otomatik olay Işleme işlemleri](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea3e8156-89a1-45b1-8bd6-938abc79fdfd) |Microsoft bu olay yanıtı denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1363.json) |

### <a name="incident-handling--dynamic-reconfiguration"></a>Olay Işleme | Dinamik yeniden yapılandırma

**Kimlik**: nıst SP 800-53 R4 IR-4 (2) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1364-olay Işleme \| dinamik yeniden yapılandırma](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4c615c2a-dc83-4dda-8220-abce7b50c9bc) |Microsoft bu olay yanıtı denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1364.json) |

### <a name="incident-handling--continuity-of-operations"></a>Olay Işleme | Işlemlerin sürekliliği

**Kimlik**: nıst SP 800-53 R4 IR-4 (3) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1365-olay Işleme \| sürekliliği](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4116891d-72f7-46ee-911c-8056cc8dcbd5) |Microsoft bu olay yanıtı denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1365.json) |

### <a name="incident-handling--information-correlation"></a>Olay Işleme | Bilgi bağıntısı

**Kimlik**: nıst SP 800-53 R4 IR-4 (4) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1366-olay Işleme \| bilgileri bağıntı](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F06c45c30-ae44-4f0f-82be-41331da911cc) |Microsoft bu olay yanıtı denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1366.json) |

### <a name="incident-handling--insider-threats---specific-capabilities"></a>Olay Işleme | Insider tehditleri özgü yetenekler

**Kimlik**: nıst SP 800-53 R4 IR-4 (6) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1367-olay Işleme \| Insider tehditlerine özgü yetenekler](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F435b2547-6374-4f87-b42d-6e8dbe6ae62a) |Microsoft bu olay yanıtı denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1367.json) |

### <a name="incident-handling--correlation-with-external-organizations"></a>Olay Işleme | Dış kuruluşlara bağıntı

**Kimlik**: nıst SP 800-53 R4 IR-4 (8) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1368- \| dış kuruluşlar Ile olay işleme bağıntısı](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F465f32da-0ace-4603-8d1b-7be5a3a702de) |Microsoft bu olay yanıtı denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1368.json) |

### <a name="incident-monitoring"></a>Olay Izleme

**Kimlik**: nıst SP 800-53 R4 IR-5 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1369-olay Izleme](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F18cc35ed-a429-486d-8d59-cb47e87304ed) |Microsoft bu olay yanıtı denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1369.json) |

### <a name="incident-monitoring--automated-tracking--data-collection--analysis"></a>Olay Izleme | Otomatik Izleme/veri toplama/analiz

**Kimlik**: nıst SP 800-53 R4 IR-5 (1) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1370-olay Izleme \| otomatik izleme/veri toplama/analiz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F924e1b2d-c502-478f-bfdb-a7e09a0d5c01) |Microsoft bu olay yanıtı denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1370.json) |

### <a name="incident-reporting"></a>Olay raporlama

**Kimlik**: nıst SP 800-53 R4 IR-6 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1371-olay raporlama](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9447f354-2c85-4700-93b3-ecdc6cb6a417) |Microsoft bu olay yanıtı denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1371.json) |
|[Microsoft yönetilen denetim 1372-olay raporlama](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F25b96717-c912-4c00-9143-4e487f411726) |Microsoft bu olay yanıtı denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1372.json) |

### <a name="incident-reporting--automated-reporting"></a>Olay raporlama | Otomatik raporlama

**Kimlik**: nıst SP 800-53 R4 IR-6 (1) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1373-olay raporlaması \| Otomatik raporlama](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4cca950f-c3b7-492a-8e8f-ea39663c14f9) |Microsoft bu olay yanıtı denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1373.json) |

### <a name="incident-response-assistance"></a>Olay yanıtı yardımı

**Kimlik**: nıst SP 800-53 R4 IR-7 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1374-olay yanıtı yardımı](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcc5c8616-52ef-4e5e-8000-491634ed9249) |Microsoft bu olay yanıtı denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1374.json) |

### <a name="incident-response-assistance--automation-support-for-availability-of-information--support"></a>Olay yanıtı yardımı | Bilgi/destek kullanılabilirliği için Otomasyon desteği

**Kimlik**: nıst SP 800-53 R4 IR-7 (1) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1375- \| bilgi ve destek kullanılabilirliği Için olay yanıtı yardım Otomasyonu desteği](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F00379355-8932-4b52-b63a-3bc6daf3451a) |Microsoft bu olay yanıtı denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1375.json) |

### <a name="incident-response-assistance--coordination-with-external-providers"></a>Olay yanıtı yardımı | Dış sağlayıcılarla koordinasyon

**Kimlik**: nıst SP 800-53 R4 IR-7 (2) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1376- \| dış sağlayıcılardan oluşan olay yanıtı yardımı koordinasyonu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F493a95f3-f2e3-47d0-af02-65e6d6decc2f) |Microsoft bu olay yanıtı denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1376.json) |
|[Microsoft yönetilen denetim 1377- \| dış sağlayıcılardan oluşan olay yanıtı yardımı koordinasyonu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F68434bd1-e14b-4031-9edb-a4adf5f84a67) |Microsoft bu olay yanıtı denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1377.json) |

### <a name="incident-response-plan"></a>Olay yanıtı planı

**Kimlik**: nıst SP 800-53 R4 IR-8 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1378-olay yanıt planı](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F97fceb70-6983-42d0-9331-18ad8253184d) |Microsoft bu olay yanıtı denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1378.json) |
|[Microsoft yönetilen denetim 1379-olay yanıt planı](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9442dd2c-a07f-46cd-b55a-553b66ba47ca) |Microsoft bu olay yanıtı denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1379.json) |
|[Microsoft yönetilen denetim 1380-olay yanıt planı](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4319b7e-ea8d-42ff-8a67-ccd462972827) |Microsoft bu olay yanıtı denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1380.json) |
|[Microsoft yönetilen denetim 1381-olay yanıt planı](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe5368258-9684-4567-8126-269f34e65eab) |Microsoft bu olay yanıtı denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1381.json) |
|[Microsoft yönetilen denetim 1382-olay yanıt planı](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F841392b3-40da-4473-b328-4cde49db67b3) |Microsoft bu olay yanıtı denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1382.json) |
|[Microsoft yönetilen denetim 1383-olay yanıt planı](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd4558451-e16a-4d2d-a066-fe12a6282bb9) |Microsoft bu olay yanıtı denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1383.json) |

### <a name="information-spillage-response"></a>Bilgi Sıçraisi yanıtı

**Kimlik**: nıst SP 800-53 R4 IR-9 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1384-bilgi Sıçraisi yanıtı](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F79fbc228-461c-4a45-9004-a865ca0728a7) |Microsoft bu olay yanıtı denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1384.json) |
|[Microsoft yönetilen denetim 1385-bilgi Sıçraisi yanıtı](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3e495e65-8663-49ca-9b38-9f45e800bc58) |Microsoft bu olay yanıtı denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1385.json) |
|[Microsoft yönetilen denetim 1386-bilgi Sıçraisi yanıtı](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5120193e-91fd-4f9d-bc6d-194f94734065) |Microsoft bu olay yanıtı denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1386.json) |
|[Microsoft yönetilen denetim 1387-bilgi Sıçraisi yanıtı](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3007185-3857-43a9-8237-06ca94f1084c) |Microsoft bu olay yanıtı denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1387.json) |
|[Microsoft yönetilen denetim 1388-bilgi Sıçraisi yanıtı](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c7c575a-d4c5-4f6f-bd49-dee97a8cba55) |Microsoft bu olay yanıtı denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1388.json) |
|[Microsoft yönetilen denetim 1389-bilgi Sıçraisi yanıtı](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc39e6fda-ae70-4891-a739-be7bba6d1062) |Microsoft bu olay yanıtı denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1389.json) |

### <a name="information-spillage-response--responsible-personnel"></a>Bilgi Sıçraisi yanıtı | Sorumlu personel

**Kimlik**: nıst SP 800-53 R4 IR-9 (1) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1390-bilgi Sıçraisi yanıtı \| sorumlu personeli](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc3b65b63-09ec-4cb5-8028-7dd324d10eb0) |Microsoft bu olay yanıtı denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1390.json) |

### <a name="information-spillage-response--training"></a>Bilgi Sıçraisi yanıtı | İtme

**Kimlik**: nıst SP 800-53 R4 IR-9 (2) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1391-Information sıvı Lajı yanıt \| eğitimi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdd6ac1a1-660e-4810-baa8-74e868e2ed47) |Microsoft bu olay yanıtı denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1391.json) |

### <a name="information-spillage-response--post-spill-operations"></a>Bilgi Sıçraisi yanıtı | Taşma sonrası Işlemler

**Kimlik**: nıst SP 800-53 R4 IR-9 (3) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1392-bilgi Sıçraisi yanıt \| sonrası işlemleri](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86dc819f-15e1-43f9-a271-41ae58d4cecc) |Microsoft bu olay yanıtı denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1392.json) |

### <a name="information-spillage-response--exposure-to-unauthorized-personnel"></a>Bilgi Sıçraisi yanıtı | Yetkisiz personele maruz olma

**Kimlik**: nıst SP 800-53 R4 IR-9 (4) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1393-yetkisiz personele bilgi verme ve yanıt \| pozlaması](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F731856d8-1598-4b75-92de-7d46235747c0) |Microsoft bu olay yanıtı denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1393.json) |

## <a name="maintenance"></a>Bakım

### <a name="system-maintenance-policy-and-procedures"></a>Sistem bakım Ilkesi ve yordamları

**Kimlik**: nıst SP 800-53 R4 MA-1 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1394-sistem bakım Ilkesi ve yordamları](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4db56f68-3f50-45ab-88f3-ca46f5379a94) |Microsoft bu bakım denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1394.json) |
|[Microsoft yönetilen denetim 1395-sistem bakım Ilkesi ve yordamları](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7207a023-a517-41c5-9df2-09d4c6845a05) |Microsoft bu bakım denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1395.json) |

### <a name="controlled-maintenance"></a>Denetlenen bakım

**Kimlik**: nıst SP 800-53 R4 MA-2 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1396 kontrollü bakım](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F276af98f-4ff9-4e69-99fb-c9b2452fb85f) |Microsoft bu bakım denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1396.json) |
|[Microsoft yönetilen denetim 1397 kontrollü bakım](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F391af4ab-1117-46b9-b2c7-78bbd5cd995b) |Microsoft bu bakım denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1397.json) |
|[Microsoft yönetilen denetim 1398 kontrollü bakım](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F443e8f3d-b51a-45d8-95a7-18b0e42f4dc4) |Microsoft bu bakım denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1398.json) |
|[Microsoft yönetilen denetim 1399 kontrollü bakım](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2256e638-eb23-480f-9e15-6cf1af0a76b3) |Microsoft bu bakım denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1399.json) |
|[Microsoft yönetilen denetim 1400 kontrollü bakım](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa96d5098-a604-4cdf-90b1-ef6449a27424) |Microsoft bu bakım denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1400.json) |
|[Microsoft yönetilen denetim 1401 kontrollü bakım](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb78ee928-e3c1-4569-ad97-9f8c4b629847) |Microsoft bu bakım denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1401.json) |

### <a name="controlled-maintenance--automated-maintenance-activities"></a>Denetlenen bakım | Otomatik bakım etkinlikleri

**Kimlik**: nıst SP 800-53 R4 MA-2 (2) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1402 kontrollü bakım \| Otomatik bakım etkinlikleri](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a560d32-8075-4fec-9615-9f7c853f4ea9) |Microsoft bu bakım denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1402.json) |
|[Microsoft yönetilen denetim 1403 kontrollü bakım \| Otomatik bakım etkinlikleri](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F57149289-d52b-4f40-9fe6-5233c1ef80f7) |Microsoft bu bakım denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1403.json) |

### <a name="maintenance-tools"></a>Bakım araçları

**Kimlik**: nıst SP 800-53 R4 ma-3 **sahipliği**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1404-bakım araçları](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F13d8f903-0cd6-449f-a172-50f6579c182b) |Microsoft bu bakım denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1404.json) |

### <a name="maintenance-tools--inspect-tools"></a>Bakım araçları | Araçları incele

**Kimlik**: nıst SP 800-53 R4 ma-3 (1) **sahipliği**: Microsoft

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1405-bakım araçları \| Inceleme araçları](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffe1a0bf3-409a-4b00-b60d-0b1f917f7e7b) |Microsoft bu bakım denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1405.json) |

### <a name="maintenance-tools--inspect-media"></a>Bakım araçları | Medyayı İncele

**Kimlik**: nıst SP 800-53 R4 ma-3 (2) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1406-bakım araçları \| medya incelemesi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa0f5339c-9292-43aa-a0bc-d27c6b8e30aa) |Microsoft bu bakım denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1406.json) |

### <a name="maintenance-tools--prevent-unauthorized-removal"></a>Bakım araçları | Yetkisiz kaldırmayı engelle

**Kimlik**: nıst SP 800-53 R4 ma-3 (3) **sahipliği**: Microsoft

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1407-bakım araçları \| yetkisiz kaldırmayı engeller](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fff9fbd83-1d8d-4b41-aac2-94cb44b33976) |Microsoft bu bakım denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1407.json) |
|[Microsoft yönetilen denetim 1408-bakım araçları \| yetkisiz kaldırmayı engeller](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc5f56ac6-4bb2-4086-bc41-ad76344ba2c2) |Microsoft bu bakım denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1408.json) |
|[Microsoft yönetilen denetim 1409-bakım araçları \| yetkisiz kaldırmayı engeller](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd1880188-e51a-4772-b2ab-68f5e8bd27f6) |Microsoft bu bakım denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1409.json) |
|[Microsoft yönetilen denetim 1410-bakım araçları \| yetkisiz kaldırmayı engeller](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa2596a9f-e59f-420d-9625-6e0b536348be) |Microsoft bu bakım denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1410.json) |

### <a name="nonlocal-maintenance"></a>Yerel olmayan bakım

**Kimlik**: nıst SP 800-53 R4 ma-4 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1411-yerel olmayan bakım](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F898d4fe8-f743-4333-86b7-0c9245d93e7d) |Microsoft bu bakım denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1411.json) |
|[Microsoft yönetilen denetim 1412-yerel olmayan bakım](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3492d949-0dbb-4589-88b3-7b59601cc764) |Microsoft bu bakım denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1412.json) |
|[Microsoft yönetilen denetim 1413-yerel olmayan bakım](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faeedddb6-6bc0-42d5-809b-80048033419d) |Microsoft bu bakım denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1413.json) |
|[Microsoft yönetilen denetim 1414-yerel olmayan bakım](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2ce63a52-e47b-4ae2-adbb-6e40d967f9e6) |Microsoft bu bakım denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1414.json) |
|[Microsoft yönetilen denetim 1415-yerel olmayan bakım](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F61a1dd98-b259-4840-abd5-fbba7ee0da83) |Microsoft bu bakım denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1415.json) |

### <a name="nonlocal-maintenance--document-nonlocal-maintenance"></a>Yerel olmayan bakım | Belge yerel olmayan bakım

**Kimlik**: nıst SP 800-53 R4 ma-4 (2) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1416-yerel olmayan bakım \| belgesi yerel olmayan bakım](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F38dfd8a3-5290-4099-88b7-4081f4c4d8ae) |Microsoft bu bakım denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1416.json) |

### <a name="nonlocal-maintenance--comparable-security--sanitization"></a>Yerel olmayan bakım | Karşılaştırılabilir güvenlik/Temizleme

**Kimlik**: nıst SP 800-53 R4 ma-4 (3) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1417-yerel olarak bakım ile \| karşılaştırılabilir güvenlik/Temizleme](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7522ed84-70d5-4181-afc0-21e50b1b6d0e) |Microsoft bu bakım denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1417.json) |
|[Microsoft yönetilen denetim 1418-yerel olarak bakım ile \| karşılaştırılabilir güvenlik/Temizleme](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F28e633fd-284e-4ea7-88b4-02ca157ed713) |Microsoft bu bakım denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1418.json) |

### <a name="nonlocal-maintenance--cryptographic-protection"></a>Yerel olmayan bakım | Şifreleme koruması

**Kimlik**: nıst SP 800-53 R4 ma-4 (6) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1419-yerel olmayan bakım \| şifreleme koruması](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6747bf9-2b97-45b8-b162-3c8becb9937d) |Microsoft bu bakım denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1419.json) |

### <a name="maintenance-personnel"></a>Bakım personeli

**Kimlik**: nıst SP 800-53 R4 ma-5 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1420-bakım personeli](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F05ae08cc-a282-413b-90c7-21a2c60b8404) |Microsoft bu bakım denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1420.json) |
|[Microsoft yönetilen denetim 1421-bakım personeli](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe539caaa-da8c-41b8-9e1e-449851e2f7a6) |Microsoft bu bakım denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1421.json) |
|[Microsoft yönetilen denetim 1422-bakım personeli](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea556850-838d-4a37-8ce5-9d7642f95e11) |Microsoft bu bakım denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1422.json) |

### <a name="maintenance-personnel--individuals-without-appropriate-access"></a>Bakım personeli | Uygun erişimi olmayan bireyler

**Kimlik**: nıst SP 800-53 R4 ma-5 (1) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1423- \| uygun erişimi olmayan bakım personeli kişileri](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7741669e-d4f6-485a-83cb-e70ce7cbbc20) |Microsoft bu bakım denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1423.json) |
|[Microsoft yönetilen denetim 1424- \| uygun erişimi olmayan bakım personeli kişileri](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf55fc87-48e1-4676-a2f8-d9a8cf993283) |Microsoft bu bakım denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1424.json) |

### <a name="timely-maintenance"></a>Zamanında bakım

**Kimlik**: nıst SP 800-53 R4 ma-6 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1425-zamanında bakım](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5983d99c-f39b-4c32-a3dc-170f19f6941b) |Microsoft bu bakım denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1425.json) |

## <a name="media-protection"></a>Medya koruması

### <a name="media-protection-policy-and-procedures"></a>Medya koruma Ilkesi ve yordamları

**Kimlik**: nıst SP 800-53 R4 MP-1 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1426-medya koruma Ilkesi ve yordamları](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F21f639bc-f42b-46b1-8f40-7a2a389c291a) |Microsoft bu medya koruma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1426.json) |
|[Microsoft yönetilen denetim 1427-medya koruma Ilkesi ve yordamları](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbc90e44f-d83f-4bdf-900f-3d5eb4111b31) |Microsoft bu medya koruma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1427.json) |

### <a name="media-access"></a>Medya erişimi

**Kimlik**: nıst SP 800-53 R4 MP-2 **sahipliği**: Microsoft

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1428-medya erişimi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a77fcc7-b8d8-451a-ab52-56197913c0c7) |Microsoft bu medya koruma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1428.json) |

### <a name="media-marking"></a>Medya Işaretleme

**Kimlik**: nıst SP 800-53 R4 MP-3 **sahipliği**: Microsoft

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1429-medya Işaretleme](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb07c9b24-729e-4e85-95fc-f224d2d08a80) |Microsoft bu medya koruma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1429.json) |
|[Microsoft yönetilen denetim 1430-medya Işaretleme](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0f559588-5e53-4b14-a7c4-85d28ebc2234) |Microsoft bu medya koruma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1430.json) |

### <a name="media-storage"></a>Medya depolama

**Kimlik**: nıst SP 800-53 R4 MP-4 **sahiplik**: Microsoft

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1431-medya depolama](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa7173c52-2b99-4696-a576-63dd5f970ef4) |Microsoft bu medya koruma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1431.json) |
|[Microsoft yönetilen denetim 1432-medya depolama](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1140e542-b80d-4048-af45-3f7245be274b) |Microsoft bu medya koruma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1432.json) |

### <a name="media-transport"></a>Medya aktarımı

**Kimlik**: nıst SP 800-53 R4 MP-5 **sahiplik**: Microsoft

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1433-medya aktarımı](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b879b41-2728-41c5-ad24-9ee2c37cbe65) |Microsoft bu medya koruma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1433.json) |
|[Microsoft yönetilen denetim 1434-medya aktarımı](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c18f06b-a68d-41c3-8863-b8cd3acb5f8f) |Microsoft bu medya koruma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1434.json) |
|[Microsoft yönetilen denetim 1435-medya aktarımı](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffa8d221b-d130-4637-ba16-501e666628bb) |Microsoft bu medya koruma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1435.json) |
|[Microsoft yönetilen denetim 1436-medya aktarımı](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F28aab8b4-74fd-4b7c-9080-5a7be525d574) |Microsoft bu medya koruma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1436.json) |

### <a name="media-transport--cryptographic-protection"></a>Medya aktarımı | Şifreleme koruması

**Kimlik**: nıst SP 800-53 R4 MP-5 (4) **sahiplik**: Microsoft

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1437-medya taşıma \| şifreleme koruması](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d1eb6ed-bf13-4046-b993-b9e2aef0f76c) |Microsoft bu medya koruma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1437.json) |

### <a name="media-sanitization"></a>Medya Temizleme

**Kimlik**: nıst SP 800-53 R4 MP-6 **sahiplik**: Microsoft

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1438-medya Temizleme](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F40fcc635-52a2-4dbc-9523-80a1f4aa1de6) |Microsoft bu medya koruma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1438.json) |
|[Microsoft yönetilen denetim 1439-medya Temizleme](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdce72873-c5f1-47c3-9b4f-6b8207fd5a45) |Microsoft bu medya koruma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1439.json) |

### <a name="media-sanitization--review--approve--track--document--verify"></a>Medya Temizleme | Gözden geçirme/onaylama/Izleme/belge/doğrulama

**Kimlik**: nıst SP 800-53 R4 MP-6 (1) **sahiplik**: Microsoft

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1440-medya Temizleme/ \| İnceleme/onaylama/izleme/belge/doğrulama](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F881299bf-2a5b-4686-a1b2-321d33679953) |Microsoft bu medya koruma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1440.json) |

### <a name="media-sanitization--equipment-testing"></a>Medya Temizleme | Ekipman testi

**Kimlik**: nıst SP 800-53 R4 MP-6 (2) **sahiplik**: Microsoft

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1441-medya Temizleme \| ekipman testi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6519d7f3-e8a2-4ff3-a935-9a9497152ad7) |Microsoft bu medya koruma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1441.json) |

### <a name="media-sanitization--nondestructive-techniques"></a>Medya Temizleme | Geri dönüşlü teknikler

**Kimlik**: nıst SP 800-53 R4 MP-6 (3) **sahiplik**: Microsoft

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1442-medya Temizleme geri \| dönüşlü teknikler](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f26049b-2c5a-4841-9ff3-d48a26aae475) |Microsoft bu medya koruma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1442.json) |

### <a name="media-use"></a>Medya kullanımı

**Kimlik**: nıst SP 800-53 R4 MP-7 **sahiplik**: Microsoft

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1443-medya kullanımı](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcd0ec6fa-a2e7-4361-aee4-a8688659a9ed) |Microsoft bu medya koruma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1443.json) |

### <a name="media-use--prohibit-use-without-owner"></a>Medya kullanımı | Sahip olmadan kullanımı yasakla

**Kimlik**: nıst SP 800-53 R4 MP-7 (1) **sahiplik**: Microsoft

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1444-medya kullanımı, \| sahip olmadan kullanımı yasaklıyor](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F666143df-f5e0-45bd-b554-135f0f93e44e) |Microsoft bu medya koruma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1444.json) |

## <a name="physical-and-environmental-protection"></a>Fiziksel ve ortam koruması

### <a name="physical-and-environmental-protection-policy-and-procedures"></a>Fiziksel ve ortam koruma Ilkesi ve yordamları

**Kimlik**: nıst SP 800-53 R4 PE-1 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1445-fiziksel ve çevre koruma Ilkesi ve yordamları](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F32d07d59-2716-4972-b37b-214a67ac4a37) |Microsoft bu fiziksel ve ortam koruma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1445.json) |
|[Microsoft yönetilen denetim 1446-fiziksel ve çevre koruma Ilkesi ve yordamları](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbf6850fe-abba-468e-9ef4-d09ec7d983cd) |Microsoft bu fiziksel ve ortam koruma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1446.json) |

### <a name="physical-access-authorizations"></a>Fiziksel erişim yetkilendirmeleri

**Kimlik**: nıst SP 800-53 R4 PE-2 **sahipliği**: Microsoft

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1447-fiziksel erişim yetkilendirmeleri](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb9783a99-98fe-4a95-873f-29613309fe9a) |Microsoft bu fiziksel ve ortam koruma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1447.json) |
|[Microsoft yönetilen denetim 1448-fiziksel erişim yetkilendirmeleri](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F825d6494-e583-42f2-a3f2-6458e6f0004f) |Microsoft bu fiziksel ve ortam koruma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1448.json) |
|[Microsoft yönetilen denetim 1449-fiziksel erişim yetkilendirmeleri](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff784d3b0-5f2b-49b7-b9f3-00ba8653ced5) |Microsoft bu fiziksel ve ortam koruma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1449.json) |
|[Microsoft yönetilen denetim 1450-fiziksel erişim yetkilendirmeleri](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F134d7a13-ba3e-41e2-b236-91bfcfa24e01) |Microsoft bu fiziksel ve ortam koruma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1450.json) |

### <a name="physical-access-control"></a>Fiziksel Access Control

**Kimlik**: nıst SP 800-53 R4 PE-3 **sahipliği**: Microsoft

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1451-fiziksel Access Control](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3f1e5a3-25c1-4476-8cb6-3955031f8e65) |Microsoft bu fiziksel ve ortam koruma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1451.json) |
|[Microsoft yönetilen denetim 1452-fiziksel Access Control](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82c76455-4d3f-4e09-a654-22e592107e74) |Microsoft bu fiziksel ve ortam koruma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1452.json) |
|[Microsoft yönetilen denetim 1453-fiziksel Access Control](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9693b564-3008-42bc-9d5d-9c7fe198c011) |Microsoft bu fiziksel ve ortam koruma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1453.json) |
|[Microsoft yönetilen denetim 1454-fiziksel Access Control](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fad58985d-ab32-4f99-8bd3-b7e134c90229) |Microsoft bu fiziksel ve ortam koruma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1454.json) |
|[Microsoft yönetilen denetim 1455-fiziksel Access Control](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F068a88d4-e520-434e-baf0-9005a8164e6a) |Microsoft bu fiziksel ve ortam koruma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1455.json) |
|[Microsoft yönetilen denetim 1456-fiziksel Access Control](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F733ba9e3-9e7c-440a-a7aa-6196a90a2870) |Microsoft bu fiziksel ve ortam koruma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1456.json) |
|[Microsoft yönetilen denetim 1457-fiziksel Access Control](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff2d9d3e6-8886-4305-865d-639163e5c305) |Microsoft bu fiziksel ve ortam koruma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1457.json) |

### <a name="physical-access-control--information-system-access"></a>Fiziksel Access Control | Bilgi sistemi erişimi

**Kimlik**: nıst SP 800-53 R4 PE-3 (1) **sahipliği**: Microsoft

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1458-fiziksel Access Control \| bilgi sistemi erişimi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8c19ceb7-56e9-4488-8ddb-b1eb3aa6d203) |Microsoft bu fiziksel ve ortam koruma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1458.json) |

### <a name="access-control-for-transmission-medium"></a>Iletim ortamı için Access Control

**Kimlik**: nıst SP 800-53 R4 PE-4 **sahiplik**: Microsoft

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1459-Iletim ortamı Access Control](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F75cc73c7-5cdb-479d-a06f-7b4d0dbb1da0) |Microsoft bu fiziksel ve ortam koruma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1459.json) |

### <a name="access-control-for-output-devices"></a>Çıkış cihazları için Access Control

**Kimlik**: nıst SP 800-53 R4 pe-5 **sahipliği**: Microsoft

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1460-çıkış cihazları Için Access Control](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6f3ce1bb-4f77-4695-8355-70b08d54fdda) |Microsoft bu fiziksel ve ortam koruma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1460.json) |

### <a name="monitoring-physical-access"></a>Fiziksel erişimi izleme

**Kimlik**: nıst SP 800-53 R4 PE-6 **sahipliği**: Microsoft

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1461-fiziksel erişimi Izleme](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faafef03e-fea8-470b-88fa-54bd1fcd7064) |Microsoft bu fiziksel ve ortam koruma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1461.json) |
|[Microsoft yönetilen denetim 1462-fiziksel erişimi Izleme](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9b1f3a9a-13a1-4b40-8420-36bca6fd8c02) |Microsoft bu fiziksel ve ortam koruma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1462.json) |
|[Microsoft yönetilen denetim 1463-fiziksel erişimi Izleme](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F59721f87-ae25-4db0-a2a4-77cc5b25d495) |Microsoft bu fiziksel ve ortam koruma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1463.json) |

### <a name="monitoring-physical-access--intrusion-alarms--surveillance-equipment"></a>Fiziksel erişimi izleme | Yetkisiz giriş uyarıları/Izleme donanımı

**Kimlik**: nıst SP 800-53 R4 PE-6 (1) **sahiplik**: Microsoft

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1464-fiziksel erişim \| Giriş uyarıları/Izleme donanımını izleme](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F41256567-1795-4684-b00b-a1308ce43cac) |Microsoft bu fiziksel ve ortam koruma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1464.json) |

### <a name="monitoring-physical-access--monitoring-physical-access-to-information-systems"></a>Fiziksel erişimi izleme | Bilgi sistemlerine fiziksel erişimi izleme

**Kimlik**: nıst SP 800-53 R4 PE-6 (4) **sahiplik**: Microsoft

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1465- \| bilgi sistemlerine fiziksel erişimi Izleme için fiziksel erişimi izleme](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe6e41554-86b5-4537-9f7f-4fc41a1d1640) |Microsoft bu fiziksel ve ortam koruma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1465.json) |

### <a name="visitor-access-records"></a>Ziyaretçi erişimi kayıtları

**Kimlik**: nıst SP 800-53 R4 PE-8 **sahipliği**: Microsoft

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1466-ziyaretçi erişim kayıtları](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0d943a9c-a6f1-401f-a792-740cdb09c451) |Microsoft bu fiziksel ve ortam koruma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1466.json) |
|[Microsoft yönetilen denetim 1467-ziyaretçi erişim kayıtları](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5350cbf9-8bdd-4904-b22a-e88be84ca49d) |Microsoft bu fiziksel ve ortam koruma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1467.json) |

### <a name="visitor-access-records--automated-records-maintenance--review"></a>Ziyaretçi erişim kayıtları | Otomatik kayıt Bakımı/Inceleme

**Kimlik**: nıst SP 800-53 R4 PE-8 (1) **sahiplik**: Microsoft

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1468-ziyaretçi erişimi kayıtları \| otomatik kayıt Bakımı/İnceleme](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F75603f96-80a1-4757-991d-5a1221765ddd) |Microsoft bu fiziksel ve ortam koruma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1468.json) |

### <a name="power-equipment-and-cabling"></a>Güç ekipmanı ve kablolama

**Kimlik**: nıst SP 800-53 R4 PE-9 **sahipliği**: Microsoft

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1469-güç ekipmanı ve kablolama](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff509c5b6-0de0-4a4e-9b2e-cd9cbf3a58fd) |Microsoft bu fiziksel ve ortam koruma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1469.json) |

### <a name="emergency-shutoff"></a>Acil bir şekilde Shutoff

**Kimlik**: nıst SP 800-53 R4 pe-10 **sahipliği**: Microsoft

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1470-acil bir Shutoff](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc89ba09f-2e0f-44d0-8095-65b05bd151ef) |Microsoft bu fiziksel ve ortam koruma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1470.json) |
|[Microsoft yönetilen denetim 1471-acil bir Shutoff](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7dd0e9ce-1772-41fb-a50a-99977071f916) |Microsoft bu fiziksel ve ortam koruma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1471.json) |
|[Microsoft yönetilen denetim 1472-acil bir Shutoff](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef869332-921d-4c28-9402-3be73e6e50c8) |Microsoft bu fiziksel ve ortam koruma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1472.json) |

### <a name="emergency-power"></a>Acil durum gücü

**Kimlik**: nıst SP 800-53 R4 PE-11 **sahipliği**: Microsoft

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1473-acil durum gücü](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd7047705-d719-46a7-8bb0-76ad233eba71) |Microsoft bu fiziksel ve ortam koruma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1473.json) |

### <a name="emergency-power--long-term-alternate-power-supply---minimal-operational-capability"></a>Acil durum gücü | Long-Term alternatif güç kaynağı-minimum Işletimsel yetenek

**Kimlik**: nıst SP 800-53 R4 PE-11 (1) **sahiplik**: Microsoft

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1474-acil güç \| Long-Term alternatif güç kaynağı-minimum Işletimsel yetenek](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F03ad326e-d7a1-44b1-9a76-e17492efc9e4) |Microsoft bu fiziksel ve ortam koruma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1474.json) |

### <a name="emergency-lighting"></a>Acil aydınlatma

**Kimlik**: nıst SP 800-53 R4 PE-12 **sahiplik**: Microsoft

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1475-acil bir aydınlatma](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34a63848-30cf-4081-937e-ce1a1c885501) |Microsoft bu fiziksel ve ortam koruma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1475.json) |

### <a name="fire-protection"></a>Koruma

**Kimlik**: nıst SP 800-53 R4 PE-13 **sahiplik**: Microsoft

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1476-yangın koruması](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0f3c4ac2-3e35-4906-a80b-473b12a622d7) |Microsoft bu fiziksel ve ortam koruma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1476.json) |

### <a name="fire-protection--detection-devices--systems"></a>Koruma Algılama cihazları/sistemleri

**Kimlik**: nıst SP 800-53 R4 PE-13 (1) **sahiplik**: Microsoft

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1477-yangın koruma \| algılama cihazları/sistemleri](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4862a63c-6c74-4a9d-a221-89af3c374503) |Microsoft bu fiziksel ve ortam koruma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1477.json) |

### <a name="fire-protection--suppression-devices--systems"></a>Koruma Gizleme cihazları/sistemleri

**Kimlik**: nıst SP 800-53 R4 PE-13 (2) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1478-yangın korumasını \| gizleme cihazları/sistemleri](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff997df46-cfbb-4cc8-aac8-3fecdaf6a183) |Microsoft bu fiziksel ve ortam koruma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1478.json) |

### <a name="fire-protection--automatic-fire-suppression"></a>Koruma Otomatik yangın gizleme

**Kimlik**: nıst SP 800-53 R4 PE-13 (3) **sahipliği**: Microsoft

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1479-yangın koruma \| Otomatik yangın gizleme](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe327b072-281d-4f75-9c28-4216e5d72f26) |Microsoft bu fiziksel ve ortam koruma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1479.json) |

### <a name="temperature-and-humidity-controls"></a>Sıcaklık ve nem denetimleri

**Kimlik**: nıst SP 800-53 R4 PE-14 **sahiplik**: Microsoft

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1480-sıcaklık ve nem denetimleri](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F18a767cc-1947-4338-a240-bc058c81164f) |Microsoft bu fiziksel ve ortam koruma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1480.json) |
|[Microsoft yönetilen denetim 1481-sıcaklık ve nem denetimleri](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F717a1c78-a267-4f56-ac58-ee6c54dc4339) |Microsoft bu fiziksel ve ortam koruma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1481.json) |

### <a name="temperature-and-humidity-controls--monitoring-with-alarms--notifications"></a>Sıcaklık ve nem denetimleri | Alarmlar/bildirimler Ile izleme

**Kimlik**: nıst SP 800-53 R4 PE-14 (2) **sahiplik**: Microsoft

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1482-sıcaklık ve nem, \| alarmlar/bildirimler ile Izlemeyi denetler](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9df4277e-8c88-4d5c-9b1a-541d53d15d7b) |Microsoft bu fiziksel ve ortam koruma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1482.json) |

### <a name="water-damage-protection"></a>Su hasar koruması

**Kimlik**: nıst SP 800-53 R4 PE-15 **sahipliği**: Microsoft

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1483-su hasar koruması](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5cb81060-3c8a-4968-bcdc-395a1801f6c1) |Microsoft bu fiziksel ve ortam koruma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1483.json) |

### <a name="water-damage-protection--automation-support"></a>Su hasar koruması | Otomasyon desteği

**Kimlik**: nıst SP 800-53 R4 PE-15 (1) **sahiplik**: Microsoft

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1484-su Hasprotection \| Otomasyonu desteği](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F486b006a-3653-45e8-b41c-a052d3e05456) |Microsoft bu fiziksel ve ortam koruma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1484.json) |

### <a name="delivery-and-removal"></a>Teslim ve kaldırma

**Kimlik**: nıst SP 800-53 R4 PE-16 **sahiplik**: Microsoft

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1485-teslim ve kaldırma](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F50301354-95d0-4a11-8af5-8039ecf6d38b) |Microsoft bu fiziksel ve ortam koruma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1485.json) |

### <a name="alternate-work-site"></a>Alternatif Iş sitesi

**Kimlik**: nıst SP 800-53 R4 PE-17 **sahiplik**: Microsoft

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1486-alternatif Iş sitesi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcb790345-a51f-43de-934e-98dbfaf9dca5) |Microsoft bu fiziksel ve ortam koruma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1486.json) |
|[Microsoft yönetilen denetim 1487-alternatif Iş sitesi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe9c3371d-c30c-4f58-abd9-30b8a8199571) |Microsoft bu fiziksel ve ortam koruma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1487.json) |
|[Microsoft yönetilen denetim 1488-alternatif Iş sitesi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd8ef30eb-a44f-47af-8524-ac19a36d41d2) |Microsoft bu fiziksel ve ortam koruma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1488.json) |

### <a name="location-of-information-system-components"></a>Bilgi sistemi bileşenlerinin konumu

**Kimlik**: nıst SP 800-53 R4 PE-18 **sahiplik**: Microsoft

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1489-bilgi sistemi bileşenlerinin konumu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9d0a794f-1444-4c96-9534-e35fc8c39c91) |Microsoft bu fiziksel ve ortam koruma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1489.json) |

## <a name="planning"></a>Planlama

### <a name="security-planning-policy-and-procedures"></a>Güvenlik planlama Ilkesi ve yordamları

**Kimlik**: nıst SP 800-53 R4 pl-1 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1490-güvenlik planlama Ilkesi ve yordamları](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9e61da80-0957-4892-b70c-609d5eaafb6b) |Microsoft bu planlama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1490.json) |
|[Microsoft yönetilen denetim 1491-güvenlik planlama Ilkesi ve yordamları](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1571dd40-dafc-4ef4-8f55-16eba27efc7b) |Microsoft bu planlama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1491.json) |

### <a name="system-security-plan"></a>Sistem güvenlik planı

**Kimlik**: nıst SP 800-53 R4 pl-2 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1492-sistem güvenlik planı](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7ad5f307-e045-46f7-8214-5bdb7e973737) |Microsoft bu planlama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1492.json) |
|[Microsoft yönetilen denetim 1493-sistem güvenlik planı](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22b469b3-fccf-42da-aa3b-a28e6fb113ce) |Microsoft bu planlama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1493.json) |
|[Microsoft yönetilen denetim 1494-sistem güvenlik planı](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9ed09d84-3311-4853-8b67-2b55dfa33d09) |Microsoft bu planlama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1494.json) |
|[Microsoft yönetilen denetim 1495-sistem güvenlik planı](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff4978d0e-a596-48e7-9f8c-bbf52554ce8d) |Microsoft bu planlama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1495.json) |
|[Microsoft yönetilen denetim 1496-sistem güvenlik planı](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ca96127-2f87-46ab-a4fc-0d2a786df1c8) |Microsoft bu planlama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1496.json) |

### <a name="system-security-plan--plan--coordinate-with-other-organizational-entities"></a>Sistem güvenlik planı | Diğer kurumsal varlıklarla plan yapın/düzenleyin

**Kimlik**: nıst SP 800-53 R4 pl-2 (3) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1497- \| diğer kuruluş varlıklarıyla sistem güvenlik planı planı/koordinatı](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2e3c5583-1729-4d36-8771-59c32f090a22) |Microsoft bu planlama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1497.json) |

### <a name="rules-of-behavior"></a>Davranış kuralları

**Kimlik**: nıst SP 800-53 R4 pl-4 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1498-davranış kuralları](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F633988b9-cf2f-4323-8394-f0d2af9cd6e1) |Microsoft bu planlama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1498.json) |
|[Microsoft yönetilen denetim 1499-davranış kuralları](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe59671ab-9720-4ee2-9c60-170e8c82251e) |Microsoft bu planlama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1499.json) |
|[Microsoft yönetilen denetim 1500-davranış kuralları](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9dd5b241-03cb-47d3-a5cd-4b89f9c53c92) |Microsoft bu planlama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1500.json) |
|[Microsoft yönetilen denetim 1501-davranış kuralları](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F88817b58-8472-4f6c-81fa-58ce42b67f51) |Microsoft bu planlama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1501.json) |

### <a name="rules-of-behavior--social-media-and-networking-restrictions"></a>Davranış kuralları | Sosyal medya ve ağ sınırlamaları

**Kimlik**: nıst SP 800-53 R4 pl-4 (1) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1502-davranış, \| sosyal medya ve ağ kısıtlamalarının kuralları](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe901375c-8f01-4ac8-9183-d5312f47fe63) |Microsoft bu planlama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1502.json) |

### <a name="information-security-architecture"></a>Bilgi güvenliği mimarisi

**Kimlik**: nıst SP 800-53 R4 pl-8 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1503-bilgi güvenliği mimarisi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc1fa9c2f-d439-4ab9-8b83-81fb1934f81d) |Microsoft bu planlama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1503.json) |
|[Microsoft yönetilen denetim 1504-bilgi güvenliği mimarisi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9e7c35d0-12d4-4e0c-80a2-8a352537aefd) |Microsoft bu planlama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1504.json) |
|[Microsoft yönetilen denetim 1505-bilgi güvenliği mimarisi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F813a10a7-3943-4fe3-8678-00dc52db5490) |Microsoft bu planlama denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1505.json) |

## <a name="personnel-security"></a>Personel güvenliği

### <a name="personnel-security-policy-and-procedures"></a>Personel Güvenlik Ilkesi ve yordamları

**Kimlik**: nıst SP 800-53 R4 PS-1 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1506-personel Güvenlik Ilkesi ve yordamları](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff7d2ff17-d604-4dd9-b607-9ecf63f28ad2) |Microsoft bu personeli güvenlik denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1506.json) |
|[Microsoft yönetilen denetim 1507-personel Güvenlik Ilkesi ve yordamları](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86ccd1bf-e7ad-4851-93ce-6ec817469c1e) |Microsoft bu personeli güvenlik denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1507.json) |

### <a name="position-risk-designation"></a>Pozisyon riski belirtimi

**Kimlik**: nıst SP 800-53 R4 PS-2 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1508-konum risk ataması](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F76f500cc-4bca-4583-bda1-6d084dc21086) |Microsoft bu personeli güvenlik denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1508.json) |
|[Microsoft yönetilen denetim 1509-konum risk ataması](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F70792197-9bfc-4813-905a-bd33993e327f) |Microsoft bu personeli güvenlik denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1509.json) |
|[Microsoft yönetilen denetim 1510-konum risk ataması](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F79da5b09-0e7e-499e-adda-141b069c7998) |Microsoft bu personeli güvenlik denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1510.json) |

### <a name="personnel-screening"></a>Personel filtreleme

**Kimlik**: nıst SP 800-53 R4 PS-3 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1511-personel filtreleme](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa9eae324-d327-4539-9293-b48e122465f8) |Microsoft bu personeli güvenlik denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1511.json) |
|[Microsoft yönetilen denetim 1512-personel filtreleme](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5a8324ad-f599-429b-aaed-f9c6e8c987a8) |Microsoft bu personeli güvenlik denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1512.json) |

### <a name="personnel-screening--information-with-special-protection-measures"></a>Personel filtreleme | Özel koruma ölçüleriyle Ilgili bilgiler

**Kimlik**: nıst SP 800-53 R4 PS-3 (3) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1513- \| özel koruma ölçüleriyle personel filtreleme bilgileri](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc416970d-b12b-49eb-8af4-fb144cd7c290) |Microsoft bu personeli güvenlik denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1513.json) |
|[Microsoft yönetilen denetim 1514- \| özel koruma ölçüleriyle personel filtreleme bilgileri](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9ed5ca00-0e43-434e-a018-7aab91461ba7) |Microsoft bu personeli güvenlik denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1514.json) |

### <a name="personnel-termination"></a>Personel sonlandırma

**Kimlik**: nıst SP 800-53 R4 PS-4 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1515-personel sonlandırma](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F02dd141a-a2b2-49a7-bcbd-ca31142f6211) |Microsoft bu personeli güvenlik denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1515.json) |
|[Microsoft yönetilen denetim 1516-personel sonlandırma](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fda3cd269-156f-435b-b472-c3af34c032ed) |Microsoft bu personeli güvenlik denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1516.json) |
|[Microsoft yönetilen denetim 1517-personel sonlandırma](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8f5ad423-50d6-4617-b058-69908f5586c9) |Microsoft bu personeli güvenlik denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1517.json) |
|[Microsoft yönetilen denetim 1518-personel sonlandırma](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0d58f734-c052-40e9-8b2f-a1c2bff0b815) |Microsoft bu personeli güvenlik denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1518.json) |
|[Microsoft yönetilen denetim 1519-personel sonlandırma](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2f13915a-324c-4ab8-b45c-2eefeeefb098) |Microsoft bu personeli güvenlik denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1519.json) |
|[Microsoft yönetilen denetim 1520-personel sonlandırma](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7f2c513b-eb16-463b-b469-c10e5fa94f0a) |Microsoft bu personeli güvenlik denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1520.json) |

### <a name="personnel-termination--automated-notification"></a>Personel sonlandırma | Otomatik bildirim

**Kimlik**: nıst SP 800-53 R4 PS-4 (2) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1521-personel sonlandırma \| Otomatik bildirimi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3cbddf9c-a3aa-4330-a0f5-4c0c1f1862e5) |Microsoft bu personeli güvenlik denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1521.json) |

### <a name="personnel-transfer"></a>Personel aktarımı

**Kimlik**: nıst SP 800-53 R4 PS-5 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1522-personel aktarımı](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F38b470cc-f939-4a15-80e0-9f0c74f2e2c9) |Microsoft bu personeli güvenlik denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1522.json) |
|[Microsoft yönetilen denetim 1523-personel aktarımı](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5577a310-2551-49c8-803b-36e0d5e55601) |Microsoft bu personeli güvenlik denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1523.json) |
|[Microsoft yönetilen denetim 1524-personel aktarımı](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F72f1cb4e-2439-4fe8-88ea-b8671ce3c268) |Microsoft bu personeli güvenlik denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1524.json) |
|[Microsoft yönetilen denetim 1525-personel aktarımı](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9be2f688-7a61-45e3-8230-e1ec93893f66) |Microsoft bu personeli güvenlik denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1525.json) |

### <a name="access-agreements"></a>Erişim anlaşmaları

**Kimlik**: nıst SP 800-53 R4 PS-6 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1526-erişim anlaşmaları](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F953e6261-a05a-44fd-8246-000e1a3edbb9) |Microsoft bu personeli güvenlik denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1526.json) |
|[Microsoft yönetilen denetim 1527-erişim anlaşmaları](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2823de66-332f-4bfd-94a3-3eb036cd3b67) |Microsoft bu personeli güvenlik denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1527.json) |
|[Microsoft yönetilen denetim 1528-erişim anlaşmaları](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdeb9797c-22f8-40e8-b342-a84003c924e6) |Microsoft bu personeli güvenlik denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1528.json) |

### <a name="third-party-personnel-security"></a>Üçüncü taraf personeli güvenliği

**Kimlik**: nıst SP 800-53 R4 PS-7 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1529-üçüncü taraf personel güvenliği](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd74fdc92-1cb8-4a34-9978-8556425cd14c) |Microsoft bu personeli güvenlik denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1529.json) |
|[Microsoft yönetilen denetim 1530-üçüncü taraf personel güvenliği](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6e8f9566-29f1-49cd-b61f-f8628a3cf993) |Microsoft bu personeli güvenlik denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1530.json) |
|[Microsoft yönetilen denetim 1531-üçüncü taraf personel güvenliği](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0643e0c-eee5-4113-8684-c608d05c5236) |Microsoft bu personeli güvenlik denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1531.json) |
|[Microsoft yönetilen denetim 1532-üçüncü taraf personel güvenliği](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa2c66299-9017-4d95-8040-8bdbf7901d52) |Microsoft bu personeli güvenlik denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1532.json) |
|[Microsoft yönetilen denetim 1533-üçüncü taraf personel güvenliği](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbba2a036-fb3b-4261-b1be-a13dfb5fbcaa) |Microsoft bu personeli güvenlik denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1533.json) |

### <a name="personnel-sanctions"></a>Personel Tasdiklerinin

**Kimlik**: nıst SP 800-53 R4 PS-8 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1534-personel Tasdiklerinin](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8b2b263e-cd05-4488-bcbf-4debec7a17d9) |Microsoft bu personeli güvenlik denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1534.json) |
|[Microsoft yönetilen denetim 1535-personel Tasdiklerinin](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9a165d2-967d-4733-8399-1074270dae2e) |Microsoft bu personeli güvenlik denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1535.json) |

## <a name="risk-assessment"></a>Risk değerlendirmesi

### <a name="risk-assessment-policy-and-procedures"></a>Risk değerlendirmesi Ilkesi ve yordamları

**Kimlik**: nıst SP 800-53 R4 Ra-1 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1536-risk değerlendirmesi Ilkesi ve yordamları](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6e40d9de-2ad4-4cb5-8945-23143326a502) |Microsoft bu risk değerlendirmesi denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1536.json) |
|[Microsoft yönetilen denetim 1537-risk değerlendirmesi Ilkesi ve yordamları](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb19454ca-0d70-42c0-acf5-ea1c1e5726d1) |Microsoft bu risk değerlendirmesi denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1537.json) |

### <a name="security-categorization"></a>Güvenlik kategorisi

**Kimlik**: nıst SP 800-53 R4 ra-2 **sahipliği**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1538-güvenlik kategorisi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1d7658b2-e827-49c3-a2ae-6d2bd0b45874) |Microsoft bu risk değerlendirmesi denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1538.json) |
|[Microsoft yönetilen denetim 1539-güvenlik kategorisi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faabb155f-e7a5-4896-a767-e918bfae2ee0) |Microsoft bu risk değerlendirmesi denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1539.json) |
|[Microsoft yönetilen denetim 1540-güvenlik kategorisi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff771f8cb-6642-45cc-9a15-8a41cd5c6977) |Microsoft bu risk değerlendirmesi denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1540.json) |

### <a name="risk-assessment"></a>Risk değerlendirmesi

**Kimlik**: nıst SP 800-53 R4 ra-3 **sahipliği**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1541-risk değerlendirmesi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F70f6af82-7be6-44aa-9b15-8b9231b2e434) |Microsoft bu risk değerlendirmesi denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1541.json) |
|[Microsoft yönetilen denetim 1542-risk değerlendirmesi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feab340d0-3d55-4826-a0e5-feebfeb0131d) |Microsoft bu risk değerlendirmesi denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1542.json) |
|[Microsoft yönetilen denetim 1543-risk değerlendirmesi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffd00b778-b5b5-49c0-a994-734ea7bd3624) |Microsoft bu risk değerlendirmesi denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1543.json) |
|[Microsoft yönetilen denetim 1544-risk değerlendirmesi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F43ced7c9-cd53-456b-b0da-2522649a4271) |Microsoft bu risk değerlendirmesi denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1544.json) |
|[Microsoft yönetilen denetim 1545-risk değerlendirmesi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3f4b171a-a56b-4328-8112-32cf7f947ee1) |Microsoft bu risk değerlendirmesi denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1545.json) |

### <a name="vulnerability-scanning"></a>Güvenlik açığı taraması

**Kimlik**: nıst SP 800-53 R4 ra-5 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Sanal makinelerinizde bir güvenlik açığı değerlendirme çözümünün etkinleştirilmesi gerekir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F501541f7-f7e7-4cd6-868c-4190fdad3ac9) |Sanal makineleri, desteklenen bir güvenlik açığı değerlendirme çözümü çalıştırıp çalıştırmadığını tespit etmek üzere denetler. Her bir siber risk ve güvenlik programının çekirdek bileşeni, güvenlik açıklarının tanımlama ve analizidir. Azure Güvenlik Merkezi 'nin standart fiyatlandırma katmanı, sanal makineleriniz için ek ücret olmadan güvenlik açığı taraması içerir. Ayrıca, güvenlik merkezi bu aracı sizin için otomatik olarak dağıtabilir. |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ServerVulnerabilityAssessment_Audit.json) |
|[Gelişmiş veri güvenliği SQL yönetilen örneği üzerinde etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |Gelişmiş veri güvenliği olmadan her SQL yönetilen örneğini denetleyin. |Auditınotexists, devre dışı |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[Gelişmiş veri güvenliği SQL sunucularınızda etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |Gelişmiş veri güvenliği olmadan SQL sunucularını denetleme |Auditınotexists, devre dışı |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |
|[Microsoft yönetilen denetim 1546-güvenlik açığı taraması](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2ce1ea7e-4038-4e53-82f4-63e8859333c1) |Microsoft bu risk değerlendirmesi denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1546.json) |
|[Microsoft yönetilen denetim 1547-güvenlik açığı taraması](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58abf9b8-c6d4-4b4b-bfb9-fe98fe295f52) |Microsoft bu risk değerlendirmesi denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1547.json) |
|[Microsoft yönetilen denetim 1548-güvenlik açığı taraması](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3afe6c78-6124-4d95-b85c-eb8c0c9539cb) |Microsoft bu risk değerlendirmesi denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1548.json) |
|[Microsoft yönetilen denetim 1549-güvenlik açığı taraması](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd6976a08-d969-4df2-bb38-29556c2eb48a) |Microsoft bu risk değerlendirmesi denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1549.json) |
|[Microsoft yönetilen denetim 1550-güvenlik açığı taraması](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F902908fb-25a8-4225-a3a5-5603c80066c9) |Microsoft bu risk değerlendirmesi denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1550.json) |
|[Makinelerinizdeki güvenlik yapılandırmasındaki güvenlik açıkları düzeltilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1e5fd5d-3e4c-4ce1-8661-7d1873ae6b15) |Yapılandırılmış temeli karşılamayan sunucular, Azure Güvenlik Merkezi tarafından öneriler olarak izlenir |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OSVulnerabilities_Audit.json) |
|[Sanal makine ölçek kümelerinizin güvenlik yapılandırmasındaki güvenlik açıkları düzeltilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3c735d8a-a4ba-4a3a-b7cf-db7754cf57f4) |Saldırılara karşı korumak için sanal makine ölçek kümelerinizin üzerindeki işletim sistemi güvenlik açıklarını denetleyin. |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssOSVulnerabilities_Audit.json) |
|[SQL veritabanlarınızdaki güvenlik açıkları düzeltilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffeedbf84-6b99-488c-acc2-71c829aa5ffc) |İzleme güvenlik açığı değerlendirmesi tarama sonuçları ve veritabanı güvenlik açıklarının nasıl düzeltileceğine ilişkin öneriler. |Auditınotexists, devre dışı |[4.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_SQLDbVulnerabilities_Audit.json) |

### <a name="vulnerability-scanning--update-tool-capability"></a>Güvenlik açığı taraması | Güncelleştirme aracı yeteneği

**Kimlik**: nıst SP 800-53 R4 ra-5 (1) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1551-güvenlik açığı tarama \| güncelleştirme aracı özelliği](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5bbda922-0172-4095-89e6-5b4a0bf03af7) |Microsoft bu risk değerlendirmesi denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1551.json) |

### <a name="vulnerability-scanning--update-by-frequency--prior-to-new-scan--when-identified"></a>Güvenlik açığı taraması | Sıklık/yeni tarama öncesinde/tanımlandığında Güncelleştir

**Kimlik**: nıst SP 800-53 R4 ra-5 (2) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1552-güncelleştirme, \| Yeni tarama öncesinde/tanımlandığında güvenlik açığı/sıklık taraması](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F43684572-e4f1-4642-af35-6b933bc506da) |Microsoft bu risk değerlendirmesi denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1552.json) |

### <a name="vulnerability-scanning--breadth--depth-of-coverage"></a>Güvenlik açığı taraması | Kapsam genişliği/kapsamı

**Kimlik**: nıst SP 800-53 R4 ra-5 (3) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1553-güvenlik açığı \| /Kapsam derinliği tarama](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9e5225fe-cdfb-4fce-9aec-0fe20dd53b62) |Microsoft bu risk değerlendirmesi denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1553.json) |

### <a name="vulnerability-scanning--discoverable-information"></a>Güvenlik açığı taraması | Bulunabilir bilgiler

**Kimlik**: nıst SP 800-53 R4 ra-5 (4) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1554-güvenlik açığı, \| bulunabilir bilgileri tarama](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F10984b4e-c93e-48d7-bf20-9c03b04e9eca) |Microsoft bu risk değerlendirmesi denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1554.json) |

### <a name="vulnerability-scanning--privileged-access"></a>Güvenlik açığı taraması | Ayrıcalıklı erişim

**Kimlik**: nıst SP 800-53 R4 ra-5 (5) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1555-ayrıcalıklı erişimi tarama güvenlik açığı \|](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5afa8cab-1ed7-4e40-884c-64e0ac2059cc) |Microsoft bu risk değerlendirmesi denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1555.json) |

### <a name="vulnerability-scanning--automated-trend-analyses"></a>Güvenlik açığı taraması | Otomatik eğilim analizleri

**Kimlik**: nıst SP 800-53 R4 ra-5 (6) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1556-güvenlik açığı \| Otomatik eğilim analizleri taraması](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F391ff8b3-afed-405e-9f7d-ef2f8168d5da) |Microsoft bu risk değerlendirmesi denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1556.json) |

### <a name="vulnerability-scanning--review-historic-audit-logs"></a>Güvenlik açığı taraması | Geçmişe ait denetim günlüklerini gözden geçirme

**Kimlik**: nıst SP 800-53 R4 ra-5 (8) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1557-güvenlik açığı tarama \| Gözden geçirme geçmiş denetim günlükleri](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F36fbe499-f2f2-41b6-880e-52d7ea1d94a5) |Microsoft bu risk değerlendirmesi denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1557.json) |

### <a name="vulnerability-scanning--correlate-scanning-information"></a>Güvenlik açığı taraması | Tarama bilgilerinin ilişkilendirilmesi

**Kimlik**: nıst SP 800-53 R4 ra-5 (10) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1558-güvenlik açığı ile \| bağıntılı tarama bilgileri](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F65592b16-4367-42c5-a26e-d371be450e17) |Microsoft bu risk değerlendirmesi denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1558.json) |

## <a name="system-and-services-acquisition"></a>Sistem ve Hizmetleri alma

### <a name="system-and-services-acquisition-policy-and-procedures"></a>Sistem ve Hizmetleri alma Ilkesi ve yordamları

**Kimlik**: nıst SP 800-53 R4 SA-1 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1559-sistem ve Hizmetleri alma Ilkesi ve yordamları](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F45692294-f074-42bd-ac54-16f1a3c07554) |Microsoft bu sistem ve hizmetler alma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1559.json) |
|[Microsoft yönetilen denetim 1560-sistem ve Hizmetleri alma Ilkesi ve yordamları](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe29e0915-5c2f-4d09-8806-048b749ad763) |Microsoft bu sistem ve hizmetler alma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1560.json) |

### <a name="allocation-of-resources"></a>Kaynakların ayrılması

**Kimlik**: nıst SP 800-53 R4 sa-2 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1561-kaynakların ayrılması](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F40364c3f-c331-4e29-b1e3-2fbe998ba2f5) |Microsoft bu sistem ve hizmetler alma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1561.json) |
|[Microsoft yönetilen denetim 1562-kaynakların ayrılması](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd4142013-7964-4163-a313-a900301c2cef) |Microsoft bu sistem ve hizmetler alma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1562.json) |
|[Microsoft yönetilen denetim 1563-kaynakların ayrılması](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9afe2edf-232c-4fdf-8e6a-e867a5c525fd) |Microsoft bu sistem ve hizmetler alma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1563.json) |

### <a name="system-development-life-cycle"></a>Sistem geliştirme yaşam döngüsü

**Kimlik**: nıst SP 800-53 R4 SA-3 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1564-sistem geliştirme yaşam döngüsü](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F157f0ef9-143f-496d-b8f9-f8c8eeaad801) |Microsoft bu sistem ve hizmetler alma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1564.json) |
|[Microsoft yönetilen denetim 1565-sistem geliştirme yaşam döngüsü](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F45ce2396-5c76-4654-9737-f8792ab3d26b) |Microsoft bu sistem ve hizmetler alma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1565.json) |
|[Microsoft yönetilen denetim 1566-sistem geliştirme yaşam döngüsü](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F50ad3724-e2ac-4716-afcc-d8eabd97adb9) |Microsoft bu sistem ve hizmetler alma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1566.json) |
|[Microsoft yönetilen denetim 1567-sistem geliştirme yaşam döngüsü](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe72edbf6-aa61-436d-a227-0f32b77194b3) |Microsoft bu sistem ve hizmetler alma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1567.json) |

### <a name="acquisition-process"></a>Alım Işlemi

**Kimlik**: nıst SP 800-53 R4 SA-4 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1568-alma Işlemi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6a8eae8-9854-495a-ac82-d2cd3eac02a6) |Microsoft bu sistem ve hizmetler alma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1568.json) |
|[Microsoft yönetilen denetim 1569-alma Işlemi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fad2f8e61-a564-4dfd-8eaa-816f5be8cb34) |Microsoft bu sistem ve hizmetler alma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1569.json) |
|[Microsoft yönetilen denetim 1570-alma Işlemi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa7fcf38d-bb09-4600-be7d-825046eb162a) |Microsoft bu sistem ve hizmetler alma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1570.json) |
|[Microsoft yönetilen denetim 1571-alma Işlemi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb11c985b-f2cd-4bd7-85f4-b52426edf905) |Microsoft bu sistem ve hizmetler alma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1571.json) |
|[Microsoft yönetilen denetim 1572-alma Işlemi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F04f5fb00-80bb-48a9-a75b-4cb4d4c97c36) |Microsoft bu sistem ve hizmetler alma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1572.json) |
|[Microsoft yönetilen denetim 1573-alma Işlemi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58c93053-7b98-4cf0-b99f-1beb985416c2) |Microsoft bu sistem ve hizmetler alma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1573.json) |
|[Microsoft yönetilen denetim 1574-alma Işlemi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0f935dab-83d6-47b8-85ef-68b8584161b9) |Microsoft bu sistem ve hizmetler alma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1574.json) |

### <a name="acquisition-process--functional-properties-of-security-controls"></a>Alım Işlemi | Güvenlik denetimlerinin işlevsel özellikleri

**Kimlik**: nıst SP 800-53 R4 SA-4 (1) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1575- \| güvenlik denetimlerinin alım işlemi Işlevsel özellikleri](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F93e1bb73-1b08-4dbe-9c62-8e2e92e7ec41) |Microsoft bu sistem ve hizmetler alma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1575.json) |

### <a name="acquisition-process--design--implementation-information-for-security-controls"></a>Alım Işlemi | Güvenlik denetimleri için tasarım/uygulama bilgileri

**Kimlik**: nıst SP 800-53 R4 SA-4 (2) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1576- \| güvenlik denetimleri Için alım işlemi tasarım/uygulama bilgileri](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5f18c885-ade3-48c5-80b1-8f9216019c18) |Microsoft bu sistem ve hizmetler alma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1576.json) |

### <a name="acquisition-process--continuous-monitoring-plan"></a>Alım Işlemi | Sürekli Izleme planı

**Kimlik**: nıst SP 800-53 R4 SA-4 (8) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1577-alma Işlemi \| sürekli Izleme planı](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd922484a-8cfc-4a6b-95a4-77d6a685407f) |Microsoft bu sistem ve hizmetler alma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1577.json) |

### <a name="acquisition-process--functions--ports--protocols--services-in-use"></a>Alım Işlemi | İşlevler/bağlantı noktaları/protokoller/hizmetler kullanımda

**Kimlik**: nıst SP 800-53 R4 SA-4 (9) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1578-alma Işlemi \| işlevleri/bağlantı noktaları/protokol/hizmet kullanımda](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F45b7b644-5f91-498e-9d89-7402532d3645) |Microsoft bu sistem ve hizmetler alma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1578.json) |

### <a name="acquisition-process--use-of-approved-piv-products"></a>Alım Işlemi | Onaylanan PIV ürünlerinin kullanımı

**Kimlik**: nıst SP 800-53 R4 SA-4 (10) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1579- \| onaylanan PIV ürünlerini alma süreci kullanımı](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4e54c7ef-7457-430b-9a3e-ef8881d4a8e0) |Microsoft bu sistem ve hizmetler alma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1579.json) |

### <a name="information-system-documentation"></a>Bilgi sistemi belgeleri

**Kimlik**: nıst SP 800-53 R4 SA-5 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1580-bilgi sistemi belgeleri](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F854db8ac-6adf-42a0-bef3-b73f764f40b9) |Microsoft bu sistem ve hizmetler alma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1580.json) |
|[Microsoft yönetilen denetim 1581-bilgi sistemi belgeleri](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F742b549b-7a25-465f-b83c-ea1ffb4f4e0e) |Microsoft bu sistem ve hizmetler alma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1581.json) |
|[Microsoft yönetilen denetim 1582-bilgi sistemi belgeleri](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcd9e2f38-259b-462c-bfad-0ad7ab4e65c5) |Microsoft bu sistem ve hizmetler alma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1582.json) |
|[Microsoft yönetilen denetim 1583-bilgi sistemi belgeleri](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0882d488-8e80-4466-bc0f-0cd15b6cb66d) |Microsoft bu sistem ve hizmetler alma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1583.json) |
|[Microsoft yönetilen denetim 1584-bilgi sistemi belgeleri](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5864522b-ff1d-4979-a9f8-58bee1fb174c) |Microsoft bu sistem ve hizmetler alma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1584.json) |

### <a name="security-engineering-principles"></a>Güvenlik Mühendisliği Ilkeleri

**Kimlik**: nıst SP 800-53 R4 sa-8 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1585-güvenlik mühendisliği Ilkeleri](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd57f8732-5cdc-4cda-8d27-ab148e1f3a55) |Microsoft bu sistem ve hizmetler alma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1585.json) |

### <a name="external-information-system-services"></a>Dış bilgi sistemi hizmetleri

**Kimlik**: nıst SP 800-53 R4 sa-9 **sahipliği**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1586-dış bilgi sistemi hizmetleri](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6e3b2fbd-8f37-4766-a64d-3f37703dcb51) |Microsoft bu sistem ve hizmetler alma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1586.json) |
|[Microsoft yönetilen denetim 1587-dış bilgi sistemi hizmetleri](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F32820956-9c6d-4376-934c-05cd8525be7c) |Microsoft bu sistem ve hizmetler alma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1587.json) |
|[Microsoft yönetilen denetim 1588-dış bilgi sistemi hizmetleri](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F68ebae26-e0e0-4ecb-8379-aabf633b51e9) |Microsoft bu sistem ve hizmetler alma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1588.json) |

### <a name="external-information-system-services--risk-assessments--organizational-approvals"></a>Dış bilgi sistemi hizmetleri | Risk değerlendirmeleri/kuruluş onayları

**Kimlik**: nıst SP 800-53 R4 sa-9 (1) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1589-dış bilgi sistemi Hizmetleri \| risk değerlendirmeleri/kuruluş onayları](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86ec7f9b-9478-40ff-8cfd-6a0d510081a8) |Microsoft bu sistem ve hizmetler alma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1589.json) |
|[Microsoft yönetilen denetim 1590-dış bilgi sistemi Hizmetleri \| risk değerlendirmeleri/kuruluş onayları](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbf296b8c-f391-4ea4-9198-be3c9d39dd1f) |Microsoft bu sistem ve hizmetler alma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1590.json) |

### <a name="external-information-system-services--identification-of-functions--ports--protocols--services"></a>Dış bilgi sistemi hizmetleri | Işlevlerin/bağlantı noktalarının/protokollerin/hizmetlerin tanımlanması

**Kimlik**: nıst SP 800-53 R4 sa-9 (2) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1591-dış bilgi sistemi Hizmetleri \| . Işlevlerin/bağlantı noktalarının/protokollerin/hizmetlerin](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff751cdb7-fbee-406b-969b-815d367cb9b3) |Microsoft bu sistem ve hizmetler alma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1591.json) |

### <a name="external-information-system-services--consistent-interests-of-consumers-and-providers"></a>Dış bilgi sistemi hizmetleri | Tüketicilere ve sağlayıcılardan oluşan tutarlı Ilgi alanları

**Kimlik**: nıst SP 800-53 R4 sa-9 (4) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1592-dış bilgi sistemi Hizmetleri \| tüketicilerinin ve sağlayıcıların tutarlı Ilgi alanları](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1d01ba6c-289f-42fd-a408-494b355b6222) |Microsoft bu sistem ve hizmetler alma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1592.json) |

### <a name="external-information-system-services--processing-storage-and-service-location"></a>Dış bilgi sistemi hizmetleri | İşleme, depolama ve hizmet konumu

**Kimlik**: nıst SP 800-53 R4 sa-9 (5) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1593-dış bilgi sistemi Hizmetleri \| işleme, depolama ve hizmet konumu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2cd0a426-b5f5-4fe0-9539-a6043cdbc6fa) |Microsoft bu sistem ve hizmetler alma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1593.json) |

### <a name="developer-configuration-management"></a>Geliştirici yapılandırma yönetimi

**Kimlik**: nıst SP 800-53 R4 SA-10 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1594-geliştirici yapılandırma yönetimi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F042ba2a1-8bb8-45f4-b080-c78cf62b90e9) |Microsoft bu sistem ve hizmetler alma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1594.json) |
|[Microsoft yönetilen denetim 1595-geliştirici yapılandırma yönetimi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1e0414e7-6ef5-4182-8076-aa82fbb53341) |Microsoft bu sistem ve hizmetler alma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1595.json) |
|[Microsoft yönetilen denetim 1596-geliştirici yapılandırma yönetimi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F21e25e01-0ae0-41be-919e-04ce92b8e8b8) |Microsoft bu sistem ve hizmetler alma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1596.json) |
|[Microsoft yönetilen denetim 1597-geliştirici yapılandırma yönetimi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F68b250ec-2e4f-4eee-898a-117a9fda7016) |Microsoft bu sistem ve hizmetler alma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1597.json) |
|[Microsoft yönetilen denetim 1598-geliştirici yapılandırma yönetimi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fae7e1f5e-2d63-4b38-91ef-bce14151cce3) |Microsoft bu sistem ve hizmetler alma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1598.json) |

### <a name="developer-configuration-management--software--firmware-integrity-verification"></a>Geliştirici yapılandırma yönetimi | Yazılım/bellenim bütünlük doğrulaması

**Kimlik**: nıst SP 800-53 R4 SA-10 (1) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1599-geliştirici yapılandırma yönetimi \| yazılım/bellenim bütünlük doğrulaması](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0004bbf0-5099-4179-869e-e9ffe5fb0945) |Microsoft bu sistem ve hizmetler alma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1599.json) |

### <a name="developer-security-testing-and-evaluation"></a>Geliştirici güvenlik testi ve değerlendirmesi

**Kimlik**: nıst SP 800-53 R4 sa-11 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1600-geliştirici güvenlik testi ve değerlendirmesi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc53f3123-d233-44a7-930b-f40d3bfeb7d6) |Microsoft bu sistem ve hizmetler alma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1600.json) |
|[Microsoft yönetilen denetim 1601-geliştirici güvenlik testi ve değerlendirmesi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ee79a0c-addf-4ce9-9b3c-d9576ed5e20e) |Microsoft bu sistem ve hizmetler alma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1601.json) |
|[Microsoft yönetilen denetim 1602-geliştirici güvenlik testi ve değerlendirmesi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fddae2e97-a449-499f-a1c8-aea4a7e52ec9) |Microsoft bu sistem ve hizmetler alma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1602.json) |
|[Microsoft yönetilen denetim 1603-geliştirici güvenlik testi ve değerlendirmesi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2b909c26-162f-47ce-8e15-0c1f55632eac) |Microsoft bu sistem ve hizmetler alma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1603.json) |
|[Microsoft yönetilen denetim 1604-geliştirici güvenlik testi ve değerlendirmesi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F44dbba23-0b61-478e-89c7-b3084667782f) |Microsoft bu sistem ve hizmetler alma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1604.json) |

### <a name="developer-security-testing-and-evaluation--static-code-analysis"></a>Geliştirici güvenlik testi ve değerlendirmesi | Statik kod analizi

**Kimlik**: nıst SP 800-53 R4 sa-11 (1) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1605-geliştirici güvenlik testi ve değerlendirme \| Statik kod analizi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0062eb8b-dc75-4718-8ea5-9bb4a9606655) |Microsoft bu sistem ve hizmetler alma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1605.json) |

### <a name="developer-security-testing-and-evaluation--threat-and-vulnerability-analyses"></a>Geliştirici güvenlik testi ve değerlendirmesi | Tehdit ve güvenlik açığı analizleri

**Kimlik**: nıst SP 800-53 R4 sa-11 (2) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1606-geliştirici güvenlik testi ve değerlendirme \| tehdidi ve güvenlik açığı analizleri](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbaa8a9a4-5bbe-4c72-98f6-a3a47ae2b1ca) |Microsoft bu sistem ve hizmetler alma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1606.json) |

### <a name="developer-security-testing-and-evaluation--dynamic-code-analysis"></a>Geliştirici güvenlik testi ve değerlendirmesi | Dinamik kod analizi

**Kimlik**: nıst SP 800-53 R4 sa-11 (8) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1607-geliştirici güvenlik testi ve \| dinamik kod analizini değerlendirme](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F976a74cf-b192-4d35-8cab-2068f272addb) |Microsoft bu sistem ve hizmetler alma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1607.json) |

### <a name="supply-chain-protection"></a>Tedarik zinciri koruması

**Kimlik**: nıst SP 800-53 R4 sa-12 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1608-tedarik zinciri koruması](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb73b7b3b-677c-4a2a-b949-ad4dc4acd89f) |Microsoft bu sistem ve hizmetler alma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1608.json) |

### <a name="development-process-standards-and-tools"></a>Geliştirme Işlemi, standartları ve araçları

**Kimlik**: nıst SP 800-53 R4 sa-15 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1609-geliştirme Işlemi, standartları ve araçları](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9e93fa71-42ac-41a7-b177-efbfdc53c69f) |Microsoft bu sistem ve hizmetler alma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1609.json) |
|[Microsoft yönetilen denetim 1610-geliştirme Işlemi, standartları ve araçları](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb9f3fb54-4222-46a1-a308-4874061f8491) |Microsoft bu sistem ve hizmetler alma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1610.json) |

### <a name="developer-provided-training"></a>Developer-Provided eğitimi

**Kimlik**: nıst SP 800-53 R4 sa-16 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1611-Developer-Provided eğitimi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffdda8a0c-ac32-43f6-b2f4-7dc1df03f43f) |Microsoft bu sistem ve hizmetler alma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1611.json) |

### <a name="developer-security-architecture-and-design"></a>Geliştirici güvenlik mimarisi ve tasarımı

**Kimlik**: nıst SP 800-53 R4 sa-17 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1612-geliştirici güvenlik mimarisi ve tasarımı](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa2037b3d-8b04-4171-8610-e6d4f1d08db5) |Microsoft bu sistem ve hizmetler alma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1612.json) |
|[Microsoft yönetilen denetim 1613-geliştirici güvenlik mimarisi ve tasarımı](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffe2ad78b-8748-4bff-a924-f74dfca93f30) |Microsoft bu sistem ve hizmetler alma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1613.json) |
|[Microsoft yönetilen denetim 1614-geliştirici güvenlik mimarisi ve tasarımı](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8154e3b3-cc52-40be-9407-7756581d71f6) |Microsoft bu sistem ve hizmetler alma denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1614.json) |

## <a name="system-and-communications-protection"></a>Sistem ve Iletişim koruması

### <a name="system-and-communications-protection-policy-and-procedures"></a>Sistem ve Iletişim koruma Ilkesi ve yordamları

**Kimlik**: nıst SP 800-53 R4 SC-1 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1615-sistem ve Iletişim koruma Ilkesi ve yordamları](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff35e02aa-0a55-49f8-8811-8abfa7e6f2c0) |Microsoft bu sistem ve Iletişim koruması denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1615.json) |
|[Microsoft yönetilen denetim 1616-sistem ve Iletişim koruma Ilkesi ve yordamları](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2006457a-48b3-4f7b-8d2e-1532287f9929) |Microsoft bu sistem ve Iletişim koruması denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1616.json) |

### <a name="application-partitioning"></a>Uygulama bölümlendirme

**Kimlik**: nıst SP 800-53 R4 SC-2 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1617-uygulama bölümlendirme](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa631d8f5-eb81-4f9d-9ee1-74431371e4a3) |Microsoft bu sistem ve Iletişim koruması denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1617.json) |

### <a name="security-function-isolation"></a>Güvenlik Işlevi yalıtımı

**Kimlik**: nıst SP 800-53 R4 SC-3 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1618-güvenlik Işlevi yalıtımı](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff52f89aa-4489-4ec4-950e-8c96a036baa9) |Microsoft bu sistem ve Iletişim koruması denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1618.json) |

### <a name="information-in-shared-resources"></a>Paylaşılan kaynaklardaki bilgiler

**Kimlik**: nıst SP 800-53 R4 SC-4 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1619-paylaşılan kaynaklardaki bilgiler](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc722e569-cb52-45f3-a643-836547d016e1) |Microsoft bu sistem ve Iletişim koruması denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1619.json) |

### <a name="denial-of-service-protection"></a>Hizmet reddi koruması

**Kimlik**: nıst SP 800-53 R4 SC-5 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Azure DDoS koruma standardı etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa7aca53f-2ed4-4466-a25e-0b45ade68efd) |Genel bir IP ile uygulama ağ geçidinin parçası olan bir alt ağa sahip tüm sanal ağlar için DDoS koruma standardı etkinleştirilmelidir. |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableDDoSProtection_Audit.json) |
|[Microsoft yönetilen denetim 1620-hizmet reddi koruması](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd17c826b-1dec-43e1-a984-7b71c446649c) |Microsoft bu sistem ve Iletişim koruması denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1620.json) |

### <a name="resource-availability"></a>Kaynak kullanılabilirliği

**Kimlik**: nıst SP 800-53 R4 SC-6 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1621-kaynak kullanılabilirliği](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3cb9f731-744a-4691-a481-ca77b0411538) |Microsoft bu sistem ve Iletişim koruması denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1621.json) |

### <a name="boundary-protection"></a>Sınır koruması

**Kimlik**: nıst SP 800-53 R4 SC-7 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Uyarlamalı ağ sağlamlaştırma önerileri internet 'e yönelik sanal makinelere uygulanmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure Güvenlik Merkezi, Internet 'e yönelik sanal makinelerin trafik düzenlerini analiz eder ve olası saldırı yüzeyini azaltan ağ güvenlik grubu kuralı önerileri sağlar |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |
|[Tüm ağ bağlantı noktaları, sanal makineniz ile ilişkili ağ güvenlik gruplarında sınırlandırılmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9daedab3-fb2d-461e-b861-71790eead4f6) |Azure Güvenlik Merkezi, bazı ağ güvenlik gruplarınızı gelen kurallarınızı çok fazla izin verecek şekilde tanımladı. Gelen kurallar ' any ' veya ' Internet ' aralıklarından erişime izin vermez. Bu, saldırganların kaynaklarınızı hedeflemesini sağlayabilir. |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnprotectedEndpoints_Audit.json) |
|[Microsoft yönetilen denetim 1622-sınır koruması](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fecf56554-164d-499a-8d00-206b07c27bed) |Microsoft bu sistem ve Iletişim koruması denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1622.json) |
|[Microsoft yönetilen denetim 1623-sınır koruması](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F02ce1b22-412a-4528-8630-c42146f917ed) |Microsoft bu sistem ve Iletişim koruması denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1623.json) |
|[Microsoft yönetilen denetim 1624-sınır koruması](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F37d079e3-d6aa-4263-a069-dd7ac6dd9684) |Microsoft bu sistem ve Iletişim koruması denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1624.json) |
|[Depolama hesapları, ağ erişimini kısıtlıyor olmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34c877ad-507e-4c82-993e-3452a6e0ad3c) |Depolama hesaplarına ağ erişimi kısıtlanmalıdır. Ağ kurallarını, yalnızca izin verilen ağların uygulamalarının depolama hesabına erişebilmesi için yapılandırın. Belirli internet veya şirket içi istemcilerden gelen bağlantılara izin vermek için, belirli Azure sanal ağlarından veya genel İnternet IP adresi aralıklarına yönelik trafiğe erişim verilebilir |Denetim, reddetme, devre dışı |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |

### <a name="boundary-protection--access-points"></a>Sınır koruma | Erişim noktaları

**Kimlik**: nıst SP 800-53 R4 SC-7 (3) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Sanal makinelerin yönetim bağlantı noktaları, tam zamanında ağ erişim denetimiyle korunmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |Olası ağ tam zamanında (JıT) erişim, Azure Güvenlik Merkezi tarafından öneriler olarak izlenir |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |
|[Microsoft yönetilen denetim 1625-sınır koruma \| erişim noktaları](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb9b66a4d-70a1-4b47-8fa1-289cec68c605) |Microsoft bu sistem ve Iletişim koruması denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1625.json) |

### <a name="boundary-protection--external-telecommunications-services"></a>Sınır koruma | Dış telekomünikasyon hizmetleri

**Kimlik**: nıst SP 800-53 R4 SC-7 (4) **sahiplik**: Microsoft

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Sanal makinelerin yönetim bağlantı noktaları, tam zamanında ağ erişim denetimiyle korunmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |Olası ağ tam zamanında (JıT) erişim, Azure Güvenlik Merkezi tarafından öneriler olarak izlenir |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |
|[Microsoft yönetilen denetim 1626-sınır koruma \| dış telekomünikasyon hizmetleri](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8f6bddd-6d67-439a-88d4-c5fe39a79341) |Microsoft bu sistem ve Iletişim koruması denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1626.json) |
|[Microsoft yönetilen denetim 1627-sınır koruma \| dış telekomünikasyon hizmetleri](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffd73310d-76fc-422d-bda4-3a077149f179) |Microsoft bu sistem ve Iletişim koruması denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1627.json) |
|[Microsoft yönetilen denetim 1628-sınır koruma \| dış telekomünikasyon hizmetleri](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F67de62b4-a737-4781-8861-3baed3c35069) |Microsoft bu sistem ve Iletişim koruması denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1628.json) |
|[Microsoft yönetilen denetim 1629-sınır koruma \| dış telekomünikasyon hizmetleri](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc171b095-7756-41de-8644-a062a96043f2) |Microsoft bu sistem ve Iletişim koruması denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1629.json) |
|[Microsoft yönetilen denetim 1630-sınır koruma \| dış telekomünikasyon hizmetleri](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3643717a-3897-4bfd-8530-c7c96b26b2a0) |Microsoft bu sistem ve Iletişim koruması denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1630.json) |

### <a name="boundary-protection--deny-by-default--allow-by-exception"></a>Sınır koruma | Varsayılan olarak Reddet/özel duruma Izin ver

**Kimlik**: nıst SP 800-53 R4 SC-7 (5) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft tarafından yönetilen denetim 1631-sınır koruması \| Varsayılan olarak Reddet/özel duruma Izin ver](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F74ae9b8e-e7bb-4c9c-992f-c535282f7a2c) |Microsoft bu sistem ve Iletişim koruması denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1631.json) |

### <a name="boundary-protection--prevent-split-tunneling-for-remote-devices"></a>Sınır koruma | Uzak cihazlar için bölünmüş tüneli önleme

**Kimlik**: nıst SP 800-53 R4 SC-7 (7) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1632-sınır koruması \| uzak cihazlar Için bölünmüş tünellemeyi engelliyor](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4ce9073a-77fa-48f0-96b1-87aa8e6091c2) |Microsoft bu sistem ve Iletişim koruması denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1632.json) |

### <a name="boundary-protection--route-traffic-to-authenticated-proxy-servers"></a>Sınır koruma | Trafiği kimliği doğrulanmış proxy sunucularına yönlendir

**Kimlik**: nıst SP 800-53 R4 SC-7 (8) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1633-sınır koruma \| trafiği kimliği doğrulanmış proxy sunucularına yönlendirme](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F07557aa0-e02f-4460-9a81-8ecd2fed601a) |Microsoft bu sistem ve Iletişim koruması denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1633.json) |

### <a name="boundary-protection--prevent-unauthorized-exfiltration"></a>Sınır koruma | Yetkisiz taşmayı engelle

**Kimlik**: nıst SP 800-53 R4 SC-7 (10) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1634-sınır koruması \| yetkisiz bir savunma oluşmasını engelliyor](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F292a7c44-37fa-4c68-af7c-9d836955ded2) |Microsoft bu sistem ve Iletişim koruması denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1634.json) |

### <a name="boundary-protection--host-based-protection"></a>Sınır koruma | Host-Based koruması

**Kimlik**: nıst SP 800-53 R4 SC-7 (12) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1635-sınır koruma \| Host-Based koruması](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F87551b5d-1deb-4d0f-86cc-9dc14cb4bf7e) |Microsoft bu sistem ve Iletişim koruması denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1635.json) |

### <a name="boundary-protection--isolation-of-security-tools--mechanisms--support-components"></a>Sınır koruma | Güvenlik araçları/mekanizmaları/destek bileşenleri yalıtımı

**Kimlik**: nıst SP 800-53 R4 SC-7 (13) **sahipliği**: Microsoft

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1636-sınır koruma \| güvenlik araçları/mekanizmaları/destek bileşenleri yalıtımı](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7b694eed-7081-43c6-867c-41c76c961043) |Microsoft bu sistem ve Iletişim koruması denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1636.json) |

### <a name="boundary-protection--fail-secure"></a>Sınır koruma | Güvenli başarısız

**Kimlik**: nıst SP 800-53 R4 SC-7 (18) **sahiplik**: Microsoft

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1637-sınır koruması \| güvenli başarısız oldu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4075bedc-c62a-4635-bede-a01be89807f3) |Microsoft bu sistem ve Iletişim koruması denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1637.json) |

### <a name="boundary-protection--dynamic-isolation--segregation"></a>Sınır koruma | Dinamik yalıtım/ayırma

**Kimlik**: nıst SP 800-53 R4 SC-7 (20) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1638-sınır koruma \| dinamik yalıtım/ayırma](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F49b99653-32cd-405d-a135-e7d60a9aae1f) |Microsoft bu sistem ve Iletişim koruması denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1638.json) |

### <a name="boundary-protection--isolation-of-information-system-components"></a>Sınır koruma | Bilgi sistemi bileşenlerinin yalıtımı

**Kimlik**: nıst SP 800-53 R4 SC-7 (21) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetimi 1639- \| bilgi sistemi bileşenlerinin sınır koruması yalıtımı](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F78e8e649-50f6-4fe3-99ac-fedc2e63b03f) |Microsoft bu sistem ve Iletişim koruması denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1639.json) |

### <a name="transmission-confidentiality-and-integrity"></a>İletim gizliliği ve bütünlüğü

**Kimlik**: nıst SP 800-53 R4 SC-8 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1640-Iletim gizliliği ve bütünlüğü](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F05a289ce-6a20-4b75-a0f3-dc8601b6acd0) |Microsoft bu sistem ve Iletişim koruması denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1640.json) |

### <a name="transmission-confidentiality-and-integrity--cryptographic-or-alternate-physical-protection"></a>İletim gizliliği ve bütünlüğü | Şifreleme veya alternatif fiziksel koruma

**Kimlik**: nıst SP 800-53 R4 SC-8 (1) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[API uygulaması yalnızca HTTPS üzerinden erişilebilir olmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |HTTPS kullanımı, sunucu/hizmet kimlik doğrulamasını sağlar ve ağ katmanı gizlice dinleme saldırılarına karşı geçiş sırasında verileri korur. |Denetim, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |
|[İşlev Uygulaması yalnızca HTTPS üzerinden erişilebilir olmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |HTTPS kullanımı, sunucu/hizmet kimlik doğrulamasını sağlar ve ağ katmanı gizlice dinleme saldırılarına karşı geçiş sırasında verileri korur. |Denetim, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |
|[Microsoft yönetilen denetim 1641-Iletim gizliliği ve bütünlük \| şifreleme ya da alternatif fiziksel koruma](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd39d4f68-7346-4133-8841-15318a714a24) |Microsoft bu sistem ve Iletişim koruması denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1641.json) |
|[Redsıs için yalnızca Azure önbelleğinize güvenli bağlantılar etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Redsıs için yalnızca SSL ile SSL aracılığıyla bağlantıların etkinleştirilmesini denetleme. Güvenli bağlantı kullanımı, sunucu ve hizmet arasında kimlik doğrulaması sağlar ve geçiş sırasında ortadaki adam, gizlice dinleme ve oturum ele geçirme gibi ağ katmanı saldırılarına karşı verileri korur |Denetim, reddetme, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|[Depolama hesaplarına güvenli aktarım etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |Depolama hesabınızda güvenli aktarım gereksinimini denetleyin. Güvenli aktarım, depolama hesabınızı yalnızca güvenli bağlantılardan (HTTPS) istekleri kabul edecek şekilde zorlayan bir seçenektir. HTTPS kullanımı, sunucu ile hizmet arasında kimlik doğrulaması sağlar ve geçiş sırasında ortadaki adam, gizlice dinleme ve oturum ele geçirme gibi ağ katmanı saldırılarına karşı verileri korur |Denetim, reddetme, devre dışı |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |
|[Web uygulaması yalnızca HTTPS üzerinden erişilebilir olmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |HTTPS kullanımı, sunucu/hizmet kimlik doğrulamasını sağlar ve ağ katmanı gizlice dinleme saldırılarına karşı geçiş sırasında verileri korur. |Denetim, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |
|[Windows Web sunucularının güvenli iletişim protokollerini kullanacak şekilde yapılandırılması gerekir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5752e6d6-1206-46d8-8ab1-ecc2f71a8112) |Internet üzerinden iletilen bilgilerin gizliliğini korumak için web sunucularınız, sektör standardı şifreleme protokolü 'nün en son sürümünü (Aktarım Katmanı Güvenliği (TLS) kullanmalıdır. TLS, makineler arasında bir bağlantıyı şifrelemek için güvenlik sertifikalarını kullanarak bir ağ üzerinden iletişimin güvenliğini sağlar. |Auditınotexists, devre dışı |[2.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecureWebProtocol_AINE.json) |

### <a name="network-disconnect"></a>Ağ bağlantısı kesme

**Kimlik**: nıst SP 800-53 R4 SC-10 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1642-ağ bağlantısı kesme](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F53397227-5ee3-4b23-9e5e-c8a767ce6928) |Microsoft bu sistem ve Iletişim koruması denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1642.json) |

### <a name="cryptographic-key-establishment-and-management"></a>Şifreleme anahtarı kurulumu ve yönetimi

**Kimlik**: nıst SP 800-53 R4 SC-12 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1643-şifreleme anahtarının kurulması ve yönetimi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d8d492c-dd7a-46f7-a723-fa66a425b87c) |Microsoft bu sistem ve Iletişim koruması denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1643.json) |

### <a name="cryptographic-key-establishment-and-management--availability"></a>Şifreleme anahtarı kurulumu ve yönetimi | Sonrası

**Kimlik**: nıst SP 800-53 R4 SC-12 (1) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1644-şifreleme anahtarı kurulumu ve Yönetimi \| kullanılabilirliği](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa7211477-c970-446b-b4af-062f37461147) |Microsoft bu sistem ve Iletişim koruması denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1644.json) |

### <a name="cryptographic-key-establishment-and-management--symmetric-keys"></a>Şifreleme anahtarı kurulumu ve yönetimi | Simetrik anahtarlar

**Kimlik**: nıst SP 800-53 R4 SC-12 (2) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1645-şifreleme anahtarı oluşturma ve Yönetimi \| simetrik anahtarlar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fafbd0baf-ff1a-4447-a86f-088a97347c0c) |Microsoft bu sistem ve Iletişim koruması denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1645.json) |

### <a name="cryptographic-key-establishment-and-management--asymmetric-keys"></a>Şifreleme anahtarı kurulumu ve yönetimi | Asimetrik anahtarlar

**Kimlik**: nıst SP 800-53 R4 SC-12 (3) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1646-şifreleme anahtarı oluşturma ve Yönetimi \| asimetrik anahtarlar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F506814fa-b930-4b10-894e-a45b98c40e1a) |Microsoft bu sistem ve Iletişim koruması denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1646.json) |

### <a name="cryptographic-protection"></a>Şifreleme koruması

**Kimlik**: nıst SP 800-53 R4 SC-13 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1647-şifreleme koruması](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F791cfc15-6974-42a0-9f4c-2d4b82f4a78c) |Microsoft bu sistem ve Iletişim koruması denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1647.json) |

### <a name="collaborative-computing-devices"></a>İşbirliğine dayalı bilgi Işlem cihazları

**Kimlik**: nıst SP 800-53 R4 SC-15 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1648-Işbirliğine dayalı bilgi Işlem cihazları](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3a9eb14b-495a-4ebb-933c-ce4ef5264e32) |Microsoft bu sistem ve Iletişim koruması denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1648.json) |
|[Microsoft yönetilen denetim 1649-Işbirliğine dayalı bilgi Işlem cihazları](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F26d292cc-b0b8-4c29-9337-68abc758bf7b) |Microsoft bu sistem ve Iletişim koruması denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1649.json) |

### <a name="public-key-infrastructure-certificates"></a>Ortak anahtar altyapısı sertifikaları

**Kimlik**: nıst SP 800-53 R4 SC-17 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1650-ortak anahtar altyapısı sertifikaları](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F201d3740-bd16-4baf-b4b8-7cda352228b7) |Microsoft bu sistem ve Iletişim koruması denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1650.json) |

### <a name="mobile-code"></a>Mobil kod

**Kimlik**: nıst SP 800-53 R4 SC-18 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1651-mobil kod](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6db63528-c9ba-491c-8a80-83e1e6977a50) |Microsoft bu sistem ve Iletişim koruması denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1651.json) |
|[Microsoft yönetilen denetim 1652-mobil kod](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6998e84a-2d29-4e10-8962-76754d4f772d) |Microsoft bu sistem ve Iletişim koruması denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1652.json) |
|[Microsoft yönetilen denetim 1653-mobil kod](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6b1c00a7-7fd0-42b0-8c5b-c45f6fa1f71b) |Microsoft bu sistem ve Iletişim koruması denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1653.json) |

### <a name="voice-over-internet-protocol"></a>Internet Protokolü üzerinden ses

**Kimlik**: nıst SP 800-53 R4 SC-19 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1654-Internet üzerinden ses Protokolü](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a2ee16e-ab1f-414a-800b-d1608835862b) |Microsoft bu sistem ve Iletişim koruması denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1654.json) |
|[Microsoft yönetilen denetim 1655-Internet üzerinden ses Protokolü](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F121eab72-390e-4629-a7e2-6d6184f57c6b) |Microsoft bu sistem ve Iletişim koruması denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1655.json) |

### <a name="secure-name--address-resolution-service-authoritative-source"></a>Güvenli ad/adres çözümleme hizmeti (yetkili Kaynak)

**Kimlik**: nıst SP 800-53 R4 SC-20 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1656-güvenli ad/adres çözümleme hizmeti (yetkili Kaynak)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1cb067d5-c8b5-4113-a7ee-0a493633924b) |Microsoft bu sistem ve Iletişim koruması denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1656.json) |
|[Microsoft yönetilen denetim 1657-güvenli ad/adres çözümleme hizmeti (yetkili Kaynak)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F90f01329-a100-43c2-af31-098996135d2b) |Microsoft bu sistem ve Iletişim koruması denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1657.json) |

### <a name="secure-name--address-resolution-service-recursive-or-caching-resolver"></a>Güvenli ad/adres çözümleme hizmeti (özyinelemeli veya önbelleğe alma Çözümleyicisi)

**Kimlik**: nıst SP 800-53 R4 SC-21 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1658-güvenli ad/adres çözümleme hizmeti (özyinelemeli veya önbelleğe alma Çözümleyicisi)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F063b540e-4bdc-4e7a-a569-3a42ddf22098) |Microsoft bu sistem ve Iletişim koruması denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1658.json) |

### <a name="architecture-and-provisioning-for-name--address-resolution-service"></a>Ad/adres çözümleme hizmeti için mimari ve sağlama

**Kimlik**: nıst SP 800-53 R4 SC-22 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1659-ad/adres çözümleme hizmeti Için mimari ve sağlama](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F35a4102f-a778-4a2e-98c2-971056288df8) |Microsoft bu sistem ve Iletişim koruması denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1659.json) |

### <a name="session-authenticity"></a>Oturum özgünlük

**Kimlik**: nıst SP 800-53 R4 SC-23 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1660-oturum özgünlük belgesi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F63096613-ce83-43e5-96f4-e588e8813554) |Microsoft bu sistem ve Iletişim koruması denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1660.json) |

### <a name="session-authenticity--invalidate-session-identifiers-at-logout"></a>Oturum özgünlük belgesi | Oturum kapatma sırasında oturum tanımlayıcılarını geçersiz kılar

**Kimlik**: nıst SP 800-53 R4 SC-23 (1) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1661-oturum kimlik doğrulaması, oturum \| kapatma sırasında oturum tanımlayıcılarını geçersiz kılar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4c643c9a-1be7-4016-a5e7-e4bada052920) |Microsoft bu sistem ve Iletişim koruması denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1661.json) |

### <a name="fail-in-known-state"></a>Bilinen durumda başarısız oldu

**Kimlik**: nıst SP 800-53 R4 SC-24 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1662-bilinen durumda başarısız oldu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F165cb91f-7ea8-4ab7-beaf-8636b98c9d15) |Microsoft bu sistem ve Iletişim koruması denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1662.json) |

### <a name="protection-of-information-at-rest"></a>Bekleyen bilgilerin korunması

**Kimlik**: nıst SP 800-53 R4 SC-28 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1663-bekleyen bilgilerin koruması](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F60171210-6dde-40af-a144-bf2670518bfa) |Microsoft bu sistem ve Iletişim koruması denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1663.json) |

### <a name="protection-of-information-at-rest--cryptographic-protection"></a>Bekleyen bilgilerin korunması | Şifreleme koruması

**Kimlik**: nıst SP 800-53 R4 SC-28 (1) **sahiplik**: müşteri

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Gelişmiş veri güvenliği SQL yönetilen örneği üzerinde etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |Gelişmiş veri güvenliği olmadan her SQL yönetilen örneğini denetleyin. |Auditınotexists, devre dışı |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[Gelişmiş veri güvenliği SQL sunucularınızda etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |Gelişmiş veri güvenliği olmadan SQL sunucularını denetleme |Auditınotexists, devre dışı |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |
|[Disk şifrelemesi sanal makinelere uygulanmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0961003e-5a0a-4549-abde-af6a37f2724d) |Etkin bir disk şifrelemesi olmayan sanal makineler, Azure Güvenlik Merkezi tarafından öneriler olarak izlenir. |Auditınotexists, devre dışı |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnencryptedVMDisks_Audit.json) |
|[Microsoft yönetilen denetim 1664-Rest \| şifreleme korumasında bilgilerin korunması](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa2cdf6b8-9505-4619-b579-309ba72037ac) |Microsoft bu sistem ve Iletişim koruması denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1664.json) |
|[SQL veritabanlarındaki Saydam Veri Şifrelemesi etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17k78e20-9358-41c9-923c-fb736d382a12) |Rest verilerini korumak ve uyumluluk gereksinimlerini karşılamak için saydam veri şifrelemesi etkinleştirilmelidir |Auditınotexists, devre dışı |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlDBEncryption_Audit.json) |

### <a name="process-isolation"></a>İşlem yalıtımı

**Kimlik**: nıst SP 800-53 R4 SC-39 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1665-Işlem yalıtımı](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5df3a55c-8456-44d4-941e-175f79332512) |Microsoft bu sistem ve Iletişim koruması denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1665.json) |

## <a name="system-and-information-integrity"></a>Sistem ve bilgi bütünlüğü

### <a name="system-and-information-integrity-policy-and-procedures"></a>Sistem ve bilgi bütünlüğü Ilkesi ve yordamları

**Kimlik**: nıst SP 800-53 R4 sı-1 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1666-sistem ve bilgi bütünlüğü Ilkesi ve yordamları](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F12e30ee3-61e6-4509-8302-a871e8ebb91e) |Microsoft bu sistem ve bilgi bütünlüğü denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1666.json) |
|[Microsoft yönetilen denetim 1667-sistem ve bilgi bütünlüğü Ilkesi ve yordamları](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd61880dc-6e38-4f2a-a30c-3406a98f8220) |Microsoft bu sistem ve bilgi bütünlüğü denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1667.json) |

### <a name="flaw-remediation"></a>Hata Düzeltme

**Kimlik**: nıst SP 800-53 R4 sı-2 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Sanal makinelerinizde bir güvenlik açığı değerlendirme çözümünün etkinleştirilmesi gerekir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F501541f7-f7e7-4cd6-868c-4190fdad3ac9) |Sanal makineleri, desteklenen bir güvenlik açığı değerlendirme çözümü çalıştırıp çalıştırmadığını tespit etmek üzere denetler. Her bir siber risk ve güvenlik programının çekirdek bileşeni, güvenlik açıklarının tanımlama ve analizidir. Azure Güvenlik Merkezi 'nin standart fiyatlandırma katmanı, sanal makineleriniz için ek ücret olmadan güvenlik açığı taraması içerir. Ayrıca, güvenlik merkezi bu aracı sizin için otomatik olarak dağıtabilir. |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ServerVulnerabilityAssessment_Audit.json) |
|[Microsoft yönetilen denetimi 1668-kusur düzeltmesi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8fb0966e-be1d-42c3-baca-60df5c0bcc61) |Microsoft bu sistem ve bilgi bütünlüğü denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1668.json) |
|[Microsoft yönetilen denetimi 1669-kusur düzeltmesi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F48f2f62b-5743-4415-a143-288adc0e078d) |Microsoft bu sistem ve bilgi bütünlüğü denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1669.json) |
|[Microsoft yönetilen denetimi 1670-kusur düzeltmesi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc6108469-57ee-4666-af7e-79ba61c7ae0c) |Microsoft bu sistem ve bilgi bütünlüğü denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1670.json) |
|[Microsoft yönetilen denetimi 1671-kusur düzeltmesi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5c5bbef7-a316-415b-9b38-29753ce8e698) |Microsoft bu sistem ve bilgi bütünlüğü denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1671.json) |
|[Sanal makine ölçek kümelerindeki sistem güncelleştirmeleri yüklenmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc3f317a7-a95c-4547-b7e7-11017ebdf2fe) |Windows ve Linux sanal makine ölçek kümelerinizin güvende olduğundan emin olmak için yüklenmesi gereken eksik sistem güvenlik güncelleştirmelerinin ve kritik güncelleştirmelerin olup olmadığını denetleyin. |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssMissingSystemUpdates_Audit.json) |
|[Makinelerinize sistem güncelleştirmeleri yüklenmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86b3d65f-7626-441e-b690-81a8b71cff60) |Sunucularınızdaki eksik güvenlik sistemi güncelleştirmeleri, Azure Güvenlik Merkezi tarafından öneriler olarak izlenir |Auditınotexists, devre dışı |[4.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingSystemUpdates_Audit.json) |
|[Makinelerinizdeki güvenlik yapılandırmasındaki güvenlik açıkları düzeltilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1e5fd5d-3e4c-4ce1-8661-7d1873ae6b15) |Yapılandırılmış temeli karşılamayan sunucular, Azure Güvenlik Merkezi tarafından öneriler olarak izlenir |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OSVulnerabilities_Audit.json) |
|[Sanal makine ölçek kümelerinizin güvenlik yapılandırmasındaki güvenlik açıkları düzeltilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3c735d8a-a4ba-4a3a-b7cf-db7754cf57f4) |Saldırılara karşı korumak için sanal makine ölçek kümelerinizin üzerindeki işletim sistemi güvenlik açıklarını denetleyin. |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssOSVulnerabilities_Audit.json) |
|[SQL veritabanlarınızdaki güvenlik açıkları düzeltilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffeedbf84-6b99-488c-acc2-71c829aa5ffc) |İzleme güvenlik açığı değerlendirmesi tarama sonuçları ve veritabanı güvenlik açıklarının nasıl düzeltileceğine ilişkin öneriler. |Auditınotexists, devre dışı |[4.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_SQLDbVulnerabilities_Audit.json) |

### <a name="flaw-remediation--central-management"></a>Hata Düzeltme | Merkezi Yönetim

**Kimlik**: nıst SP 800-53 R4 sı-2 (1) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1672-hata düzeltme \| Merkezi Yönetimi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb45fe972-904e-45a4-ac20-673ba027a301) |Microsoft bu sistem ve bilgi bütünlüğü denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1672.json) |

### <a name="flaw-remediation--automated-flaw-remediation-status"></a>Hata Düzeltme | Otomatik kusur düzeltme durumu

**Kimlik**: nıst SP 800-53 R4 sı-2 (2) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetimi 1673-kusur düzeltme \| otomatik kusur düzeltme durumu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdff0b90d-5a6f-491c-b2f8-b90aa402d844) |Microsoft bu sistem ve bilgi bütünlüğü denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1673.json) |

### <a name="flaw-remediation--time-to-remediate-flaws--benchmarks-for-corrective-actions"></a>Hata Düzeltme | Düzeltici eylemler için kusurların/kıyaslamaların düzeltilmesi için geçen süre

**Kimlik**: nıst SP 800-53 R4 sı-2 (3) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1674- \| düzeltici eylemler için kusurların/kıyaslamaların düzeltilmesi Için hata düzeltme süresi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F93e9e233-dd0a-4bde-aea5-1371bce0e002) |Microsoft bu sistem ve bilgi bütünlüğü denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1674.json) |
|[Microsoft yönetilen denetim 1675- \| düzeltici eylemler için kusurların/kıyaslamaların düzeltilmesi Için hata düzeltme süresi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffacb66e0-1c48-478a-bed5-747a312323e1) |Microsoft bu sistem ve bilgi bütünlüğü denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1675.json) |

### <a name="malicious-code-protection"></a>Kötü amaçlı kod koruması

**Kimlik**: nıst SP 800-53 R4 sı-3 **sahipliği**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Uç nokta koruma çözümü, sanal makine ölçek kümelerine yüklenmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F26a828e1-e88f-464e-bbb3-c134a282b9de) |Tehdit ve güvenlik açıklarına karşı korumak için sanal makinelerinizdeki bir Endpoint Protection çözümünün varlığını ve sistem durumunu denetleyin. |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssMissingEndpointProtection_Audit.json) |
|[Microsoft yönetilen denetim 1676-kötü amaçlı kod koruması](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc10fb58b-56a8-489e-9ce3-7ffe24e78e4b) |Microsoft bu sistem ve bilgi bütünlüğü denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1676.json) |
|[Microsoft yönetilen denetim 1677-kötü amaçlı kod koruması](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4a248e1e-040f-43e5-bff2-afc3a57a3923) |Microsoft bu sistem ve bilgi bütünlüğü denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1677.json) |
|[Microsoft yönetilen denetim 1678-kötü amaçlı kod koruması](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdd533cb0-b416-4be7-8e86-4d154824dfd7) |Microsoft bu sistem ve bilgi bütünlüğü denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1678.json) |
|[Microsoft yönetilen denetim 1679-kötü amaçlı kod koruması](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2cf42a28-193e-41c5-98df-7688e7ef0a88) |Microsoft bu sistem ve bilgi bütünlüğü denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1679.json) |
|[Azure Güvenlik Merkezi 'nde eksik Endpoint Protection izleme](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf6cd1bd-1635-48cb-bde7-5b15693900b9) |Yüklü bir Endpoint Protection Aracısı olmayan sunucular, Azure Güvenlik Merkezi tarafından öneriler olarak izlenir |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingEndpointProtection_Audit.json) |

### <a name="malicious-code-protection--central-management"></a>Kötü amaçlı kod koruma | Merkezi Yönetim

**Kimlik**: nıst SP 800-53 R4 sı-3 (1) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Uç nokta koruma çözümü, sanal makine ölçek kümelerine yüklenmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F26a828e1-e88f-464e-bbb3-c134a282b9de) |Tehdit ve güvenlik açıklarına karşı korumak için sanal makinelerinizdeki bir Endpoint Protection çözümünün varlığını ve sistem durumunu denetleyin. |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssMissingEndpointProtection_Audit.json) |
|[Microsoft yönetilen denetim 1680-kötü amaçlı kod koruma \| Merkezi Yönetimi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F399cd6ee-0e18-41db-9dea-cde3bd712f38) |Microsoft bu sistem ve bilgi bütünlüğü denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1680.json) |
|[Azure Güvenlik Merkezi 'nde eksik Endpoint Protection izleme](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf6cd1bd-1635-48cb-bde7-5b15693900b9) |Yüklü bir Endpoint Protection Aracısı olmayan sunucular, Azure Güvenlik Merkezi tarafından öneriler olarak izlenir |Auditınotexists, devre dışı |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingEndpointProtection_Audit.json) |

### <a name="malicious-code-protection--automatic-updates"></a>Kötü amaçlı kod koruma | Otomatik Güncelleştirmeler

**Kimlik**: nıst SP 800-53 R4 sı-3 (2) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1681-kötü amaçlı kod koruma \| Otomatik Güncelleştirmeler](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F12623e7e-4736-4b2e-b776-c1600f35f93a) |Microsoft bu sistem ve bilgi bütünlüğü denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1681.json) |

### <a name="malicious-code-protection--nonsignature-based-detection"></a>Kötü amaçlı kod koruma | Nonsignature-Based algılama

**Kimlik**: nıst SP 800-53 R4 sı-3 (7) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1682-kötü amaçlı kod koruma \| Nonsignature-Based algılama](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F62b638c5-29d7-404b-8d93-f21e4b1ce198) |Microsoft bu sistem ve bilgi bütünlüğü denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1682.json) |

### <a name="information-system-monitoring"></a>Bilgi sistemi Izleme

**Kimlik**: nıst SP 800-53 R4 sı-4 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Gelişmiş veri güvenliği SQL yönetilen örneği üzerinde etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |Gelişmiş veri güvenliği olmadan her SQL yönetilen örneğini denetleyin. |Auditınotexists, devre dışı |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[Gelişmiş veri güvenliği SQL sunucularınızda etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |Gelişmiş veri güvenliği olmadan SQL sunucularını denetleme |Auditınotexists, devre dışı |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |
|[VM için Log Analytics çalışma alanını denetleme-rapor uyumsuzluğu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff47b5582-33ec-4c5c-87c0-b010a6b2e917) |İlke/girişim atamasında belirtilen Log Analytics çalışma alanına oturum açtıklarında VM 'Leri uyumsuz olarak raporlar. |denetlenmesini |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalytics_WorkspaceMismatch_VM_Audit.json) |
|[Listelenen sanal makine görüntüleri için Log Analytics Aracısı etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F32133ab0-ee4b-4b44-98d6-042180979d50) |Sanal makine görüntüsü tanımlanan listede değilse ve aracı yüklü değilse, sanal makineleri uyumsuz olarak raporlar. |Auditınotexists, devre dışı |[2.0.0-Önizleme](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalytics_OSImage_Audit.json) |
|[Log Analytics aracısının, listelenen sanal makine görüntüleri için sanal makine ölçek kümelerinde etkinleştirilmesi gerekir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5c3bc7b8-a64c-4e08-a9cd-7ff0f31e1138) |Sanal makine görüntüsü tanımlanmış listede değilse ve aracı yüklü değilse, sanal makine ölçek kümelerini uyumlu değil olarak raporlar. |Auditınotexists, devre dışı |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalytics_OSImage_VMSS_Audit.json) |
|[Microsoft yönetilen denetim 1683-bilgi sistemi Izleme](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8c79fee4-88dd-44ce-bbd4-4de88948c4f8) |Microsoft bu sistem ve bilgi bütünlüğü denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1683.json) |
|[Microsoft yönetilen denetim 1684-bilgi sistemi Izleme](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F16bfdb59-db38-47a5-88a9-2e9371a638cf) |Microsoft bu sistem ve bilgi bütünlüğü denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1684.json) |
|[Microsoft yönetilen denetim 1685-bilgi sistemi Izleme](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F36b0ef30-366f-4b1b-8652-a3511df11f53) |Microsoft bu sistem ve bilgi bütünlüğü denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1685.json) |
|[Microsoft yönetilen denetim 1686-bilgi sistemi Izleme](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe17085c5-0be8-4423-b39b-a52d3d1402e5) |Microsoft bu sistem ve bilgi bütünlüğü denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1686.json) |
|[Microsoft yönetilen denetim 1687-bilgi sistemi Izleme](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7a87fc7f-301e-49f3-ba2a-4d74f424fa97) |Microsoft bu sistem ve bilgi bütünlüğü denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1687.json) |
|[Microsoft yönetilen denetim 1688-bilgi sistemi Izleme](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F063c3f09-e0f0-4587-8fd5-f4276fae675f) |Microsoft bu sistem ve bilgi bütünlüğü denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1688.json) |
|[Microsoft yönetilen denetim 1689-bilgi sistemi Izleme](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fde901f2f-a01a-4456-97f0-33cda7966172) |Microsoft bu sistem ve bilgi bütünlüğü denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1689.json) |

### <a name="information-system-monitoring--system-wide-intrusion-detection-system"></a>Bilgi sistemi Izleme | System-Wide yetkisiz giriş algılama sistemi

**Kimlik**: nıst SP 800-53 R4 sı-4 (1) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1690-bilgi sistemi Izleme \| System-Wide yetkisiz giriş algılama sistemi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa2567a23-d1c3-4783-99f3-d471302a4d6b) |Microsoft bu sistem ve bilgi bütünlüğü denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1690.json) |

### <a name="information-system-monitoring--automated-tools-for-real-time-analysis"></a>Bilgi sistemi Izleme | Real-Time çözümleme için otomatikleştirilmiş araçlar

**Kimlik**: nıst SP 800-53 R4 sı-4 (2) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1691- \| Real-Time analiz için otomatik araçlar bilgi sistemi izleme](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F71475fb4-49bd-450b-a1a5-f63894c24725) |Microsoft bu sistem ve bilgi bütünlüğü denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1691.json) |

### <a name="information-system-monitoring--inbound-and-outbound-communications-traffic"></a>Bilgi sistemi Izleme | Gelen ve giden Iletişim trafiği

**Kimlik**: nıst SP 800-53 R4 sı-4 (4) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1692-bilgi sistemi Izleme \| gelen ve giden Iletişim trafiği](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7ecda928-9df4-4dd7-8f44-641a91e470e8) |Microsoft bu sistem ve bilgi bütünlüğü denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1692.json) |

### <a name="information-system-monitoring--system-generated-alerts"></a>Bilgi sistemi Izleme | System-Generated uyarılar

**Kimlik**: nıst SP 800-53 R4 sı-4 (5) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1693-bilgi sistemi Izleme \| System-Generated uyarıları](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa450eba6-2efc-4a00-846a-5804a93c6b77) |Microsoft bu sistem ve bilgi bütünlüğü denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1693.json) |

### <a name="information-system-monitoring--analyze-communications-traffic-anomalies"></a>Bilgi sistemi Izleme | Iletişim trafiği Bozukluklerini çözümleyin

**Kimlik**: nıst SP 800-53 R4 sı-4 (11) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1694-bilgi sistemi Izleme \| Iletişim trafiği bozukluklerini analiz eder](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F426c4ac9-ff17-49d0-acd7-a13c157081c0) |Microsoft bu sistem ve bilgi bütünlüğü denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1694.json) |

### <a name="information-system-monitoring--wireless-intrusion-detection"></a>Bilgi sistemi Izleme | Kablosuz yetkisiz giriş algılama

**Kimlik**: nıst SP 800-53 R4 sı-4 (14) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1695-bilgi sistemi Izleme \| Kablosuz yetkisiz giriş algılama](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F13fcf812-ec82-4eda-9b89-498de9efd620) |Microsoft bu sistem ve bilgi bütünlüğü denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1695.json) |

### <a name="information-system-monitoring--correlate-monitoring-information"></a>Bilgi sistemi Izleme | Izleme bilgilerinin ilişkilendirilmesi

**Kimlik**: nıst SP 800-53 R4 sı-4 (16) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1696-bilgi sistemi Izleme \| bilgilerini Izleme bilgileri](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F69d2a238-20ab-4206-a6dc-f302bf88b1b8) |Microsoft bu sistem ve bilgi bütünlüğü denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1696.json) |

### <a name="information-system-monitoring--analyze-traffic--covert-exfiltration"></a>Bilgi sistemi Izleme | Trafik/Covert exfiltration çözümleme

**Kimlik**: nıst SP 800-53 R4 sı-4 (18) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1697-bilgi sistemi Izleme \| trafiği çözümleme/Covert exfiltration](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9873db2-18ad-46b3-a11a-1a1f8cbf0335) |Microsoft bu sistem ve bilgi bütünlüğü denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1697.json) |

### <a name="information-system-monitoring--individuals-posing-greater-risk"></a>Bilgi sistemi Izleme | Daha fazla risk veren bireyler

**Kimlik**: nıst SP 800-53 R4 sı-4 (19) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1698-bilgi sistemi Izleme \| bireyleri daha fazla risk taşır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F31b752c1-05a9-432a-8fce-c39b56550119) |Microsoft bu sistem ve bilgi bütünlüğü denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1698.json) |

### <a name="information-system-monitoring--privileged-users"></a>Bilgi sistemi Izleme | Ayrıcalıklı kullanıcılar

**Kimlik**: nıst SP 800-53 R4 sı-4 (20) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1699-bilgi sistemi Izleme \| ayrıcalıklı kullanıcılar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F69c7bee8-bc19-4129-a51e-65a7b39d3e7c) |Microsoft bu sistem ve bilgi bütünlüğü denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1699.json) |

### <a name="information-system-monitoring--unauthorized-network-services"></a>Bilgi sistemi Izleme | Yetkisiz ağ hizmetleri

**Kimlik**: nıst SP 800-53 R4 sı-4 (22) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1700-bilgi sistemi Izleme \| yetkisiz ağ hizmetleri](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7831b4ba-c3f4-4cb1-8c11-ef8d59438cd5) |Microsoft bu sistem ve bilgi bütünlüğü denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1700.json) |

### <a name="information-system-monitoring--host-based-devices"></a>Bilgi sistemi Izleme | Host-Based cihazlar

**Kimlik**: nıst SP 800-53 R4 sı-4 (23) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1701-bilgi sistemi Izleme \| Host-Based cihazları](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff25bc08f-27cb-43b6-9a23-014d00700426) |Microsoft bu sistem ve bilgi bütünlüğü denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1701.json) |

### <a name="information-system-monitoring--indicators-of-compromise"></a>Bilgi sistemi Izleme | Uzlaşmaya yönelik göstergeler

**Kimlik**: nıst SP 800-53 R4 sı-4 (24) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1702-bilgi sistemi Izleme \| uzlaşması göstergeleri](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4dfc0855-92c4-4641-b155-a55ddd962362) |Microsoft bu sistem ve bilgi bütünlüğü denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1702.json) |

### <a name="security-alerts-advisories-and-directives"></a>Güvenlik uyarıları, Danışma belgeleri ve yönergeler

**Kimlik**: nıst SP 800-53 R4 sı-5 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1703-güvenlik uyarıları, Danışma belgeleri ve yönergeler](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F804faf7d-b687-40f7-9f74-79e28adf4205) |Microsoft bu sistem ve bilgi bütünlüğü denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1703.json) |
|[Microsoft yönetilen denetim 1704-güvenlik uyarıları, Danışma belgeleri ve yönergeler](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2d44b6fa-1134-4ea6-ad4e-9edb68f65429) |Microsoft bu sistem ve bilgi bütünlüğü denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1704.json) |
|[Microsoft yönetilen denetim 1705-güvenlik uyarıları, Danışma belgeleri ve yönergeler](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff82e3639-fa2b-4e06-a786-932d8379b972) |Microsoft bu sistem ve bilgi bütünlüğü denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1705.json) |
|[Microsoft yönetilen denetim 1706-güvenlik uyarıları, Danışma belgeleri ve yönergeler](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff475ee0e-f560-4c9b-876b-04a77460a404) |Microsoft bu sistem ve bilgi bütünlüğü denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1706.json) |

### <a name="security-alerts-advisories-and-directives--automated-alerts-and-advisories"></a>Güvenlik uyarıları, Danışma belgeleri ve yönergeler | Otomatik uyarılar ve Danışma belgeleri

**Kimlik**: nıst SP 800-53 R4 sı-5 (1) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1707-güvenlik uyarıları, Danışma belgeleri ve yönergeler \| otomatik uyarılar ve Danışma belgeleri](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffd4a2ac8-868a-4702-a345-6c896c3361ce) |Microsoft bu sistem ve bilgi bütünlüğü denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1707.json) |

### <a name="security-function-verification"></a>Güvenlik Işlevi doğrulaması

**Kimlik**: nıst SP 800-53 R4 sı-6 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1708-güvenlik Işlevi doğrulaması](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7a1e2c88-13de-4959-8ee7-47e3d74f1f48) |Microsoft bu sistem ve bilgi bütünlüğü denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1708.json) |
|[Microsoft yönetilen denetim 1709-güvenlik Işlevi doğrulaması](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F025992d6-7fee-4137-9bbf-2ffc39c0686c) |Microsoft bu sistem ve bilgi bütünlüğü denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1709.json) |
|[Microsoft yönetilen denetim 1710-güvenlik Işlevi doğrulaması](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf2a93c8-e6dd-4c94-acdd-4a2eedfc478e) |Microsoft bu sistem ve bilgi bütünlüğü denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1710.json) |
|[Microsoft yönetilen denetim 1711-güvenlik Işlevi doğrulaması](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb083a535-a66a-41ec-ba7f-f9498bf67cde) |Microsoft bu sistem ve bilgi bütünlüğü denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1711.json) |

### <a name="software-firmware-and-information-integrity"></a>Yazılım, bellenim ve bilgi bütünlüğü

**Kimlik**: nıst SP 800-53 R4 sı-7 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1712-yazılım, bellenim ve bilgi bütünlüğü](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F44e543aa-41db-42aa-98eb-8a5eb1db53f0) |Microsoft bu sistem ve bilgi bütünlüğü denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1712.json) |

### <a name="software-firmware-and-information-integrity--integrity-checks"></a>Yazılım, bellenim ve bilgi bütünlüğü | Bütünlük denetimleri

**Kimlik**: nıst SP 800-53 R4 sı-7 (1) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1713-yazılım, bellenim ve bilgi tutarlılığı \| bütünlük denetimleri](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0d87c70b-5012-48e9-994b-e70dd4b8def0) |Microsoft bu sistem ve bilgi bütünlüğü denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1713.json) |

### <a name="software-firmware-and-information-integrity--automated-notifications-of-integrity-violations"></a>Yazılım, bellenim ve bilgi bütünlüğü | Bütünlük Ihlallerinin otomatikleştirilmiş bildirimleri

**Kimlik**: nıst SP 800-53 R4 sı-7 (2) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1714-yazılım, bellenim ve bilgi bütünlüğü \| bütünlük Ihlallerinin otomatikleştirilmiş bildirimleri](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe12494fa-b81e-4080-af71-7dbacc2da0ec) |Microsoft bu sistem ve bilgi bütünlüğü denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1714.json) |

### <a name="software-firmware-and-information-integrity--automated-response-to-integrity-violations"></a>Yazılım, bellenim ve bilgi bütünlüğü | Bütünlük Ihlallerine otomatik yanıt verme

**Kimlik**: nıst SP 800-53 R4 sı-7 (5) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1715-yazılım, bellenim ve bilgi bütünlüğü \| bütünlük Ihlallerine otomatik yanıt verme](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdd469ae0-71a8-4adc-aafc-de6949ca3339) |Microsoft bu sistem ve bilgi bütünlüğü denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1715.json) |

### <a name="software-firmware-and-information-integrity--integration-of-detection-and-response"></a>Yazılım, bellenim ve bilgi bütünlüğü | Algılama ve yanıt tümleştirmesi

**Kimlik**: nıst SP 800-53 R4 sı-7 (7) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1716-yazılım, bellenim ve \| algılama ve yanıtın bilgi tutarlılığı tümleştirmesi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe54c325e-42a0-4dcf-b105-046e0f6f590f) |Microsoft bu sistem ve bilgi bütünlüğü denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1716.json) |

### <a name="software-firmware-and-information-integrity--binary-or-machine-executable-code"></a>Yazılım, bellenim ve bilgi bütünlüğü | İkili veya makine yürütülebilir kodu

**Kimlik**: nıst SP 800-53 R4 sı-7 (14) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1717-yazılım, bellenim ve bilgi bütünlüğü \| Ikili veya makine yürütülebilir kodu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F967773fc-d9ab-4a4e-8ff6-f5e9e3f5dbef) |Microsoft bu sistem ve bilgi bütünlüğü denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1717.json) |
|[Microsoft yönetilen denetim 1718-yazılım, bellenim ve bilgi bütünlüğü \| Ikili veya makine yürütülebilir kodu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0dced7ab-9ce5-4137-93aa-14c13e06ab17) |Microsoft bu sistem ve bilgi bütünlüğü denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1718.json) |

### <a name="spam-protection"></a>İstenmeyen posta koruması

**Kimlik**: nıst SP 800-53 R4 sı-8 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1719-Istenmeyen posta koruması](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc13da9b4-fe14-4fe2-853a-5997c9d4215a) |Microsoft bu sistem ve bilgi bütünlüğü denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1719.json) |
|[Microsoft yönetilen denetim 1720-Istenmeyen posta koruması](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F44b9a7cd-f36a-491a-a48b-6d04ae7c4221) |Microsoft bu sistem ve bilgi bütünlüğü denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1720.json) |

### <a name="spam-protection--central-management"></a>İstenmeyen postadan koruma | Merkezi Yönetim

**Kimlik**: nıst SP 800-53 R4 sı-8 (1) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1721-Istenmeyen postadan koruma \| Merkezi Yönetimi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd207aaef-7c4d-4f8c-9dce-4d62dfa3d29a) |Microsoft bu sistem ve bilgi bütünlüğü denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1721.json) |

### <a name="spam-protection--automatic-updates"></a>İstenmeyen postadan koruma | Otomatik Güncelleştirmeler

**Kimlik**: nıst SP 800-53 R4 sı-8 (2) **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1722-Istenmeyen postadan koruma \| Otomatik Güncelleştirmeler](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1da06bd-25b6-4127-a301-c313d6873fff) |Microsoft bu sistem ve bilgi bütünlüğü denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1722.json) |

### <a name="information-input-validation"></a>Bilgi girişi doğrulaması

**Kimlik**: nıst SP 800-53 R4 sı-10 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1723-bilgi girişi doğrulaması](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe91927a0-ac1d-44a0-95f8-5185f9dfce9f) |Microsoft bu sistem ve bilgi bütünlüğü denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1723.json) |

### <a name="error-handling"></a>Hata İşleme

**Kimlik**: nıst SP 800-53 R4 sı-11 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1724-hata Işleme](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd07594d1-0307-4c08-94db-5d71ff31f0f6) |Microsoft bu sistem ve bilgi bütünlüğü denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1724.json) |
|[Microsoft yönetilen denetim 1725-hata Işleme](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fafc234b5-456b-4aa5-b3e2-ce89108124cc) |Microsoft bu sistem ve bilgi bütünlüğü denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1725.json) |

### <a name="information-handling-and-retention"></a>Bilgi Işleme ve bekletme

**Kimlik**: nıst SP 800-53 R4 sı-12 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1726-bilgi Işleme ve bekletme](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbaff1279-05e0-4463-9a70-8ba5de4c7aa4) |Microsoft bu sistem ve bilgi bütünlüğü denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1726.json) |

### <a name="memory-protection"></a>Bellek koruması

**Kimlik**: nıst SP 800-53 R4 sı-16 **sahiplik**: paylaşılan

|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Microsoft yönetilen denetim 1727-bellek koruması](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F697175a7-9715-4e89-b98b-c6f605888fa3) |Microsoft bu sistem ve bilgi bütünlüğü denetimini uygular |denetlenmesini |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1727.json) |

> [!NOTE]
> Belirli Azure Ilke tanımlarının kullanılabilirliği, Azure Kamu ve diğer ulusal bulutlarda farklılık gösterebilir.

## <a name="next-steps"></a>Sonraki adımlar

Azure Ilkesi ile ilgili ek makaleler:

- [Mevzuata uyumluluğuna](../concepts/regulatory-compliance.md) genel bakış.
- [Girişim tanımı yapısına](../concepts/initiative-definition-structure.md)bakın.
- [Azure ilke örneklerinde](./index.md)diğer örnekleri gözden geçirin.
- [İlkenin etkilerini anlama](../concepts/effects.md) konusunu gözden geçirin.
- [Uyumlu olmayan kaynakları nasıl düzelteceğinizi](../how-to/remediate-resources.md)öğrenin.
