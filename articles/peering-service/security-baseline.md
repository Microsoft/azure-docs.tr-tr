---
title: Microsoft Azure eşleme hizmeti için Azure Güvenlik temeli
description: Microsoft Azure eşleme hizmeti güvenlik temeli, Azure Güvenlik kıyaslaması 'nda belirtilen güvenlik önerilerini uygulamaya yönelik yordamsal kılavuz ve kaynaklar sağlar.
author: msmbaldwin
ms.service: peering-service
ms.topic: conceptual
ms.date: 04/09/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 854c669861c6aff71861481843bff97fdb7157ab
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107315704"
---
# <a name="azure-security-baseline-for-microsoft-azure-peering-service"></a>Microsoft Azure eşleme hizmeti için Azure Güvenlik temeli

Bu güvenlik temeli, [Azure Güvenlik kıyaslama sürümü 2,0](../security/benchmarks/overview.md) ' den Microsoft Azure eşleme hizmetine kılavuzluk uygular. Azure Güvenlik Karşılaştırması, Azure üzerindeki bulut çözümlerinizin güvenliğini sağlamaya yönelik öneriler sunar. İçerik, Azure Güvenlik kıyaslaması tarafından tanımlanan **güvenlik denetimlerine** ve Microsoft Azure eşleme hizmeti için geçerli olan ilgili kılavuza göre gruplandırılır.

> [!NOTE]
> Microsoft Azure eşleme hizmetine veya sorumluluğun Microsoft 'un ne olduğu ile ilgili olan **denetimler** hariç tutulur. Microsoft Azure eşleme hizmetinin Azure Güvenlik kıyaslaması ile tamamen nasıl eşlendiğini görmek için, **[tam Microsoft Azure eşleme hizmeti güvenlik temeli eşleme dosyasına](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)** bakın.

## <a name="network-security"></a>Ağ Güvenliği

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Ağ Güvenliği](/azure/security/benchmarks/security-controls-v2-network-security).*

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5: izinsiz giriş algılama/yetkisiz erişim önleme sistemleri (KIMLIKLER/IP 'ler) dağıtma

**Rehberlik**: eşleme hizmeti yerel yetkisiz giriş algılama veya önleme hizmetleri sunmaz. Müşteriler, iş gereksinimlerine uygun olarak, Azure Güvenlik Duvarı 'ndan gelen ve bilinen kötü amaçlı IP adreslerinden ve etki alanlarından gelen trafiği uyarmak ve engellemek gibi en iyi güvenlik uygulamalarını izlemelidir. Bu IP adresleri ve etki alanları Microsoft Threat-Intelligence akışından kaynaklıdır. 

Yük incelemesinin gerekli olduğu durumlarda, Azure Marketi 'nden yük İnceleme özelliklerine sahip bir üçüncü taraf yetkisiz giriş algılama veya önleme sistemleri (KIMLIKLER/IP 'ler) dağıtın.  
Alternatif olarak, ağ tabanlı yetkisiz giriş algılama veya önleme sistemleriyle birlikte (veya yerine) ana bilgisayar tabanlı bir uç nokta algılama ve yanıt (EDR) çözümü kullanın.  

Yetkisiz giriş algılama veya yetkisiz erişim önleme sisteminin kullanılması için bir yasal gereksinim varsa, yüksek kaliteli uyarılar kullanmak veya sağlamak için her zaman ayarlanmış olduğundan emin olun. 

- [Azure Güvenlik duvarını dağıtma](../firewall/tutorial-firewall-deploy-portal.md)

- [Azure Marketi, üçüncü taraf KIMLIKLERI özelliklerini içerir](https://azuremarketplace.microsoft.com/marketplace?search=IDS)

- [Microsoft Defender ATP EDR özelliği](/windows/security/threat-protection/microsoft-defender-atp/overview-endpoint-detection-response)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="identity-management"></a>Kimlik Yönetimi

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Kimlik Yönetimi](/azure/security/benchmarks/security-controls-v2-identity-management).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Azure Active Directory'yi standart merkezi kimlik ve kimlik doğrulaması sistemi haline getirin

**Rehberlik**: Microsoft Azure eşleme hizmeti kaynaklarınızın denetim düzlemine (örneğin, Azure Portal) yönetici erişimi olan kullanıcı hesaplarının envanterini saklayın.

Azure rol tabanlı erişim denetimi 'ni (Azure RBAC) yapılandırmak için aboneliğinizin Azure portal kimlik ve erişim denetimi (ıAM) bölmesini kullanın. Roller, kullanıcılar, gruplar, hizmet sorumluları ve Azure Active Directory (Azure AD) içindeki yönetilen kimliklere uygulanır.

- [Azure portalını kullanarak Azure rol ataması ekleme veya kaldırma](../role-based-access-control/role-assignments-portal.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2: Uygulama kimliklerini güvenli ve otomatik bir şekilde yönetin

**Rehberlik**: Yönetilen kimlikler, eşleme hizmeti için desteklenmez. Eşleme hizmeti gibi yönetilen kimlikleri desteklemeyen hizmetler için, bunun yerine kaynak düzeyinde kısıtlanmış izinlerle bir hizmet sorumlusu oluşturmak üzere Azure Active Directory (Azure AD) kullanın. 

- [Azure Yönetilen kimlikler](../active-directory/managed-identities-azure-resources/overview.md)

- [Azure kaynakları için yönetilen kimlikleri destekleyen hizmetler](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

- [Azure hizmet sorumlusu](/powershell/azure/create-azure-service-principal-azureps)

- [Sertifikalarla hizmet sorumlusu oluşturma](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: Uygulama erişimi için Azure AD çoklu oturum açma (SSO) özelliğini kullanın

**Rehberlik**: Microsoft Azure eşleme hizmeti, Azure kaynaklarına kimlik ve erişim yönetimi sağlamak için Azure Active Directory (Azure AD) kullanır. Buna çalışanlar gibi kuruluş kimliklerinin yanı sıra iş ortakları, satıcılar ve tedarikçiler gibi dış kimlikler de dahildir. 

Şirket içinde ve bulutta kuruluşunuzun verilerine ve kaynaklarına erişimi yönetmek ve güvenli hale getirmek için çoklu oturum açma kullanın. Tüm kullanıcılarınızı, uygulamalarınızı ve cihazlarınızı Azure AD'ye bağlayarak sorunsuz ve güvenli erişimin yanı sıra daha fazla görünürlük ve denetim elde edebilirsiniz.

- [Azure AD ile uygulamalar için SSO yaklaşımını anlama](../active-directory/manage-apps/what-is-single-sign-on.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Tüm Azure Active Directory tabanlı erişim girişimleri için güçlü kimlik doğrulaması denetimleri kullanın

**Rehberlik**: Azure Active Directory (Azure AD) ile çok faktörlü kimlik doğrulamasını etkinleştirin ve Azure Güvenlik Merkezi 'nden kimlik ve erişim yönetimi önerilerini izleyin.

- [Azure 'da çok faktörlü kimlik doğrulamasını etkinleştirme](../active-directory/authentication/howto-mfa-getstarted.md)

- [Azure Güvenlik Merkezi 'nde kimliği ve erişimi izleme](../security-center/security-center-identity-access.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: Hesap anomalilerini izleyin ve uyarı oluşturun

**Rehberlik**: ortamda şüpheli veya güvenli olmayan bir etkinlik oluştuğunda günlükler ve uyarılar oluşturmak için Azure Active Directory (Azure AD) ile PRIVILEGED IDENTITY Management (PIM) kullanın. Ayrıca, riskli Kullanıcı davranışında uyarıları ve raporları görüntülemek için Azure AD risk algılamalarını kullanın.

- [Privileged Identity Management dağıtma (PıM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Azure AD risk algılamalarını anlama](../active-directory/identity-protection/overview-identity-protection.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: Azure kaynaklarına erişimi koşullara göre kısıtlayın

**Kılavuz**: çok faktörlü kimlik doğrulaması kullanmak IÇIN belirli IP aralıklarından Kullanıcı oturumu açma gerekliliği gibi Kullanıcı tanımlı koşullara göre daha ayrıntılı erişim denetimi için Azure Active Directory (Azure AD) ile koşullu erişim kullanın. Ayrıntılı bir kimlik doğrulama oturumu yönetimi, farklı kullanım durumları için Azure AD koşullu erişim ilkesi aracılığıyla da kullanılabilir. 

- [Azure koşullu erişimine genel bakış](../active-directory/conditional-access/overview.md)

- [Ortak koşullu erişim ilkeleri](../active-directory/conditional-access/concept-conditional-access-policy-common.md)

- [Erişim Denetimi ile kimlik doğrulama oturum yönetimini yapılandırma](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="im-7-eliminate-unintended-credential-exposure"></a>IM-7: Kimlik bilgilerinin istenmeden açığa çıkma olasılığını ortadan kaldırın

**Rehberlik**: kod içinde kullanılan kimlik bilgilerini tanımlamak Için Azure DevOps Içinde kimlik bilgisi tarayıcısı uygulayın. Kimlik bilgisi tarayıcısı, bulunan kimlik bilgilerinin Azure Key Vault gibi daha güvenli konumlara taşınmasını da önerir.

GitHub için yerel gizli dizi tarama özelliğini kullanarak kod içindeki kimlik bilgilerini veya diğer gizli dizileri bulabilirsiniz.

- [Kimlik bilgisi tarayıcısı kurulumu](https://secdevtools.azurewebsites.net/helpcredscan.html)

- [GitHub'da gizli dizi tarama](https://docs.github.com/github/administering-a-repository/about-secret-scanning)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="privileged-access"></a>Ayrıcalıklı Erişim

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Ayrıcalıklı Erişim](/azure/security/benchmarks/security-controls-v2-privileged-access).*

### <a name="pa-1-protect-and-limit-highly-privileged-users"></a>PA-1: Yüksek ayrıcalıklı kullanıcıları koruyun ve sınırlayın

**Rehberlik**: yüksek ayrıcalıklı kullanıcı hesabı sayısını sınırlayın ve bu hesapları yükseltilmiş bir düzeyde koruyun. 

Azure Active Directory (Azure AD) ' deki en kritik yerleşik roller, genel yönetici ve ayrıcalıklı rol yöneticisidir çünkü bu iki role atanan kullanıcılar yönetici rollerine temsilci olarak atanabilir. 

Bu ayrıcalıklarla, kullanıcılar Azure ortamınızdaki her kaynağı doğrudan veya dolaylı olarak okuyabilir ve değiştirebilir:

- Genel yönetici/Şirket Yöneticisi: Bu role sahip olan kullanıcılar, Azure AD 'deki tüm yönetim özelliklerine, ayrıca Azure AD kimliklerini kullanan hizmetlere erişebilir.

- Ayrıcalıklı rol yöneticisi: Bu role sahip olan kullanıcılar, Azure AD 'de rol atamalarını ve Azure AD Privileged Identity Management (PıM) içinde yönetebilir. Ayrıca, bu rol, PıM ve yönetim birimlerinin tüm yönlerinin yönetimine izin verir.

Kendisine atanan belirli ayrıcalıklı izinlerle özel roller kullanıyorsanız, yönetilmeleri gereken diğer kritik roller olabilir. Önemli iş varlıklarının yönetici hesabına benzer denetimler uygulayın.  

Azure AD Privileged Identity Management (PıM) kullanarak Azure kaynaklarına ve Azure AD 'ye tam zamanında (JıT) ayrıcalıklı erişimi etkinleştirin. JIT yalnızca kullanıcıların ihtiyacı olduğunda ayrıcalıklı görevler gerçekleştirmeleri için geçici izinler verir. Azure AD kuruluşunuzda güvenli olmayan veya şüpheli etkinlikler olduğunda da PIM güvenlik uyarıları oluşturabilir.

- [Azure AD'de yönetici rolü izinleri](/azure/active-directory/users-groups-roles/directory-assign-admin-roles)

- [Azure Privileged Identity Management güvenlik uyarılarını kullanma](../active-directory/privileged-identity-management/pim-how-to-configure-security-alerts.md)

- [Azure AD'de karma ve bulut dağıtımları için ayrıcalıklı erişim güvenliğini sağlama](/azure/active-directory/users-groups-roles/directory-admin-roles-secure)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: İş açısından kritik sistemlere yönetici erişimini kısıtlayın

**Rehberlik**: ait oldukları aboneliklere ve yönetim gruplarına ayrıcalıklı erişim verilmiş hesapları kısıtlayarak, iş açısından kritik sistemlere erişimi yalıtın. 

Active Directory Etki Alanı Denetleyicileri (DC), güvenlik araçları ve iş açısından kritik sistemlerde yüklü aracılarla sistem yönetimi araçları gibi iş açısından kritik varlıklarınıza yönetici erişimi olan yönetim, kimlik ve güvenlik sistemlerine erişimi kısıtlayın. Bu yönetim ve güvenlik sistemlerine zarar veren saldırganlar, iş açısından kritik varlıkların güvenliğini tehlikeye atabilir. 

Tutarlı erişim denetimi sağlamak için, tüm erişim denetimi türleri kurumsal segmentasyon stratejinize hizalanmalıdır. 

E-posta, göz atma ve üretkenlik görevlerinde kullanılan standart kullanıcı hesaplarından ayrı ayrıcalıklı ayrıcalıklı hesaplar atadığınızdan emin olun.

- [Azure bileşenleri ve başvuru modeli](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Yönetim grubu erişimi](https://docs.microsoft.com/azure/governance/management-groups/overview#management-group-access)

- [Azure abonelik yöneticileri](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: Kullanıcı erişimini düzenli olarak gözden geçirin ve mutabık kılın

**Rehberlik**: Azure ACTIVE DIRECTORY (ad) eski hesapları keşfetmenize yardımcı olacak Günlükler sağlar. Ayrıca, grup üyeliklerini etkin bir şekilde yönetmek, kurumsal uygulamalara erişmek ve rol atamaları için Azure kimlik erişimi Incelemelerini kullanın. Yalnızca doğru kullanıcıların erişmeye devam ettiğinden emin olmak için, Kullanıcı erişimi düzenli olarak incelenebilir.

- [Azure AD etkinlik raporlarını bulun](../active-directory/reports-monitoring/howto-find-activity-reports.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: Azure AD'de acil durum erişimini ayarlayın

**Rehberlik**: Azure Active Directory (Azure AD) kuruluşunuzdan yanlışlıkla kilitlenmesini engellemek için, normal yönetim hesapları kullanılamadığında, erişim için bir acil durum hesabı ayarlayın. Acil durum erişim hesapları, daha üst düzey ayrıcalıklar içerebilir ve belirli kişilere atanmamalıdır. Acil durum erişim hesaplarını acil durum veya ' kesme camı ' senaryolarına sınırlayın.

Acil durum erişim hesapları için kimlik bilgilerinin (parola, sertifika veya akıllı kart gibi) güvenli tutulduğundan ve yalnızca acil aracı durumlarında bunları kullanma yetkisine sahip olan bireyler için bilindiğinden emin olun.

- [Azure AD'deki acil durum erişim hesaplarını yönetme](/azure/active-directory/users-groups-roles/directory-emergency-access)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="pa-5-automate-entitlement-management"></a>PA-5: yetkilendirme yönetimini otomatikleştirme 

**Rehberlik**: erişim atamaları, incelemeler ve süre sonu dahil olmak üzere erişim isteği iş akışlarını otomatikleştirmek için Azure Active Directory (Azure AD) yetkilendirme yönetimi özelliklerini kullanın. Çift veya çok aşamalı onay de desteklenir.

- [Azure Active Directory (Azure AD) erişim gözden geçirmeleri nelerdir](../active-directory/governance/access-reviews-overview.md)

- [Azure Active Directory (Azure AD) yetkilendirme yönetimi nedir?](../active-directory/governance/entitlement-management-overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: Ayrıcalıklı erişim iş istasyonlarını kullanın

**Kılavuz**: Azure Sentinel ile ilgili kaynaklarınızın oturum açmasını ve yapılandırmasını etkinleştirmek üzere Azure Active Directory (Azure AD) ile çok faktörlü kimlik doğrulaması Ile ayrıcalıklı erişim iş istasyonu (Paw) kullanın.

- [Ayrıcalıklı Erişim İş İstasyonları](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

- [Bulut tabanlı bir Azure AD çok faktörlü kimlik doğrulama dağıtımı planlama](../active-directory/authentication/howto-mfa-getstarted.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>AE-7: Tam yetecek kadar yönetim uygulama (en düşük ayrıcalık ilkesi) 

**Kılavuz**: Azure rol tabanlı erişim denetimi (Azure RBAC), Azure kaynak erişimini rol atamaları aracılığıyla yönetmenizi sağlar. Kullanıcılara, Grup hizmeti sorumlularına ve yönetilen kimliklere bu rolleri atayabilirsiniz. Belirli kaynaklar için önceden tanımlı yerleşik roller vardır ve bu roller Azure CLı, Azure PowerShell ve Azure portal gibi araçlarla envantere alınabilir veya sorgulanabilir.

Azure RBAC aracılığıyla kaynaklara atadığınız ayrıcalıklar, her zaman roller için gerekdiklere göre sınırlandırılmalıdır. Sınırlı ayrıcalıklar Azure Active Directory (Azure AD) Privileged Identity Management (PıM) ' nin tam zamanında (JıT) yaklaşımını tamamlar ve bu ayrıcalıkların düzenli olarak gözden geçirilmesi gerekir.

Yerleşik rolleri kullanarak izin verin ve yalnızca gerektiğinde özel rol oluşturun.

- [Azure rol tabanlı erişim denetimi nedir (Azure RBAC)](../role-based-access-control/overview.md)

- [Azure RBAC 'yi yapılandırma](../role-based-access-control/role-assignments-portal.md)

- [Azure AD kimlik ve erişim gözden geçirmelerini kullanma](../active-directory/governance/access-reviews-overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="asset-management"></a>Varlık Yönetimi

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Varlık Yönetimi](/azure/security/benchmarks/security-controls-v2-asset-management).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>VY-1: Güvenlik ekibinin varlıklara yönelik risklerde görünürlük elde etmesini sağlama

**Rehber**: Güvenlik ekiplerinin Azure Güvenlik Merkezi’ni kullanarak güvenlik risklerini izlemesini sağlamak için bu ekiplere Azure kiracınız ve abonelikleriniz üzerinde Güvenlik Okuyucusu izinleri verildiğinden emin olun. 

Güvenlik ekibi sorumluluklarının yapılandırmasına bağlı olarak, güvenlik risklerinin izlenmesi merkezi güvenlik ekibinin veya yerel bir ekibin sorumluluğunda olabilir. Gerçi güvenlik içgörüleri ve risklerinin kuruluş içinde her zaman merkezi bir konumda toplanması gerekir. 

Güvenlik Okuyucusu izinleri kiracının tamamına (Kök Yönetim Grubu) geniş kapsamlı olarak uygulanabileceği gibi, izinlerin kapsamı yönetim gruplarıyla veya belirli aboneliklerle de sınırlanabilir. 

İş yüklerinin ve hizmetlerin görünürlüğünü almak için ek izinler gerekebilir. 

- [Güvenlik Okuyucusu Rolüne Genel Bakış](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#security-reader)

- [Azure Yönetim Gruplarına Genel Bakış](../governance/management-groups/overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: Güvenlik ekibinin varlık envanterine ve meta verilerine erişebildiğinden emin olun

**Rehberlik**: güvenlik ekiplerinin Azure 'daki varlıkların sürekli güncelleştirilmiş envanterini erişimi olduğundan emin olun. Güvenlik ekipleri genellikle kuruluşlarının ortaya çıkabilecek risklerden olası etkilenme durumunu değerlendirmek ve sürekli güvenlik geliştirmelerine yönelik giriş sağlamak için bu envantere ihtiyaç duyar. 

Azure Güvenlik Merkezi envanter özelliği ve Azure Kaynak Grafiği, aboneliklerinizde Azure Hizmetleri, uygulamalar ve ağ kaynakları dahil tüm kaynakları sorgulayabilir ve bulabilir.  

Azure'daki etiketleri ve diğer meta verileri (ad, açıklama ve kategori) kullanarak varlıkları kuruluşunuzun sınıflandırma uygulamalarına göre mantıksal olarak düzenleyin.  

- [Azure Kaynak Grafı Gezgini ile sorgu oluşturma](../governance/resource-graph/first-query-portal.md)

- [Azure Güvenlik Merkezi varlık envanteri yönetimi](../security-center/asset-inventory.md)

- [Varlıkları etiketleme hakkında daha fazla bilgi için bkz. Kaynak adlandırma ve etiketleme karar Kılavuzu](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: Yalnızca onaylı Azure hizmetlerini kullanın

**Rehberlik**: Microsoft Azure eşleme hizmeti, ' Microsoft. eşleme/peerler ' ad alanında Azure ilkesi kullanılarak Azure Resource Manager tabanlı dağıtımları ve yapılandırma zorlamayı destekler. Kullanıcılarınızın ortamınızda sağlayabileceği hizmetleri denetlemek ve kısıtlamak için Azure İlkesi'ni kullanın. Abonelikler içindeki kaynakları sorgulamak ve bulmak için Azure Kaynak Grafı'nı kullanın. Ayrıca Azure İzleyici'yi kullanarak onaylanmamış hizmetler algılandığında uyarı tetikleme amacıyla kurallar oluşturabilirsiniz.

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Azure Ilkesiyle belirli bir kaynak türünü reddetme](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

- [Azure Kaynak Grafı Gezgini ile sorgu oluşturma](../governance/resource-graph/first-query-portal.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>ÖÖ-5: kullanıcıların Azure Resource Manager etkileşime geçme yeteneğini sınırlayın

**Rehberlik**: "Microsoft Azure yönetimi" uygulaması için "erişimi engelle" yapılandırarak kullanıcıların Azure Resource Manager etkileşime geçmesini sınırlamak üzere Azure koşullu erişimini yapılandırın.

- [Azure Resource Manager erişimi engellemek için koşullu erişimi yapılandırma](../role-based-access-control/conditional-access-azure-management.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="logging-and-threat-detection"></a>Günlüğe Kaydetme ve Tehdit Algılama

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Günlüğe Kaydetme ve Tehdit Algılama](/azure/security/benchmarks/security-controls-v2-logging-threat-protection).*

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: Azure kaynakları için tehdit algılamayı etkinleştirme

**Rehberlik**: olası tehditler ve bozukluklar için farklı türlerde Azure varlıklarını izlemediğinizden emin olun. Analistlerin sıralama için yanlış pozitif durumları azaltmaya yönelik yüksek kaliteli uyarılar almaya odaklanın. Uyarılar, günlük verilerinden, aracılardan veya diğer verilerden kaynaklıdır.

Azure hizmet telemetrisini izlemeyi ve hizmet günlüklerini çözümlemeyi temel alan Azure Güvenlik Merkezi 'nin yerleşik tehdit algılama özelliğini kullanın. Veriler, sistemden güvenlikle ilgili çeşitli yapılandırma ve olay günlüklerini okuyan ve analiz için verileri çalışma alanınıza kopyalayan Log Analytics Aracı kullanılarak toplanır. 

Ayrıca, ortamınızda belirli ölçütlerle eşleşen tehditleri araya analizi kuralları oluşturmak için Azure Sentinel kullanın. Kurallar, her olayı araştırmak için ölçütler eşleştiğinde olaylar oluşturur. Azure Sentinel, tehdit algılama yeteneklerini geliştirmek için üçüncü taraf tehdit bilgilerini de içeri aktarabilir. 

- [Azure Güvenlik Merkezi’nde tehdit koruması](/azure/security-center/threat-protection)

- [Azure Güvenlik Merkezi güvenlik uyarıları başvuru kılavuzu](../security-center/alerts-reference.md)

- [Tehditleri algılamak için özel analiz kuralları oluşturma](../sentinel/tutorial-detect-threats-custom.md)

- [Azure Sentinel ile Cyber Threat Intelligence](/azure/architecture/example-scenario/data/sentinel-threat-intelligence)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Azure kimlik ve erişim yönetimi için tehdit algılamayı etkinleştirin

**Rehberlik**: Azure Active Directory (Azure AD), Azure AD raporlama ile görüntülenebilen veya Azure izleyici ile tümleştirilen Kullanıcı günlükleri sağlar. Bu Günlükler, daha karmaşık izleme ve analiz kullanım örnekleri için Azure Sentinel veya diğer güvenlik bilgileri ve olay yönetimi (SıEM) çözümü veya izleme araçlarıyla da tümleştirilebilir:

- Oturum açma-oturum açma raporu, yönetilen uygulamaların kullanımı ve Kullanıcı oturum açma etkinlikleri hakkında bilgi sağlar

- Denetim günlükleri-Azure AD içindeki çeşitli özelliklerle yapılan tüm değişiklikler için Günlükler aracılığıyla izlenebilirlik sağlar. Denetim günlüklerine örnek olarak, Azure AD 'de Kullanıcı, uygulama, Grup, rol ve ilke ekleme veya kaldırma gibi herhangi bir kaynak üzerinde yapılan değişiklikler verilebilir

- Riskli oturum açma-riskli oturum açma, bir kullanıcı hesabının meşru sahibi olmayan birisi tarafından gerçekleştirilmiş olabilecek oturum açma girişimine yönelik bir göstergedir

- Risk için işaretlenen kullanıcılar-riskli bir Kullanıcı, tehlikeye girmiş olabilecek bir kullanıcı hesabı göstergesidir

Azure Güvenlik Merkezi Ayrıca, bir abonelikte kullanımdan kaldırılan hesapların çok fazla sayıda başarısız kimlik doğrulaması girişimi gibi bazı şüpheli etkinliklerle ilgili uyarı verebilir. Temel güvenlik durumu izlemeye ek olarak, güvenlik merkezi 'ndeki tehdit koruması modülü, bireysel Azure işlem kaynaklarından (sanal makineler, kapsayıcılar, App Service), veri kaynakları (SQL DB ve depolama gibi) ve Azure hizmet katmanları için de ayrıntılı ek güvenlik uyarıları toplayabilir. Bu özellik, hesap bozuklularını tek tek kaynaklar içinde görmenizi sağlar.

- [Azure AD 'de denetim etkinlik raporları](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Azure Kimlik Koruması’nı etkinleştirme](../active-directory/identity-protection/overview-identity-protection.md)

- [Azure Güvenlik Merkezi’nde tehdit koruması](/azure/security-center/threat-protection)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: Azure ağ etkinlikleri için günlüğe kaydetmeyi etkinleştirin

**Rehberlik**: bağlantı Telemetriyi yapılandırma

Microsoft Azure eşleme hizmeti aracılığıyla Microsoft ağı.

- [Bağlantı telemetrisini yapılandırma](measure-connection-telemetry.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Azure kaynakları için günlüğe kaydetmeyi etkinleştirin

**Kılavuz**: Azure etkinlik günlüğünü etkinleştirin ve günlükleri arşivleme için bir Log Analytics çalışma alanına, Azure Olay Hub 'ına veya Azure depolama hesabına gönderin 

Etkinlik günlükleri, denetim düzlemi düzeyinde Azure ExpressRoute kaynaklarınız üzerinde gerçekleştirilen işlemlere ilişkin öngörüler sağlar. Azure etkinlik günlüğü verilerini kullanarak, kaynaklarınız için denetim düzlemi düzeyinde gerçekleştirilen herhangi bir yazma işlemi (PUT, POST, DELETE) için "ne, kim ve ne zaman" seçeneğini belirleyebilirsiniz.

- [Azure etkinlik günlüğü](/azure/azure-monitor/platform/activity-log)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: Güvenlik günlüğü yönetim ve analiz süreçlerini merkezileştirin

**Rehberlik**: bağıntı sağlamak için günlük depolamayı ve çözümlemeyi merkezileştirme. Her günlük kaynağı için bir veri sahibi, erişim Kılavuzu, depolama konumu, verileri işlemek ve erişmek için kullanılan araçları ve veri saklama gereksinimlerini atadığınızdan emin olun.

Azure etkinlik günlüklerini merkezi günlüklerinizi tümleştirdiğinizden emin olun. Endpoint cihazları, ağ kaynakları ve diğer güvenlik sistemleri tarafından oluşturulan güvenlik verilerini toplamak için Azure Izleyici aracılığıyla günlük alma. Azure Izleyici 'de analiz sorgulamak ve gerçekleştirmek için Log Analytics çalışma alanlarını kullanın ve uzun süreli ve arşiv depolama için Azure depolama hesaplarını kullanın.

Ayrıca, Azure Sentinel 'e veya bir üçüncü taraf SıEM 'e verileri etkinleştirin ve ekleyin.

Birçok kuruluş, daha az sıklıkta kullanılan "soğuk" verileri için sık kullanılan ve Azure depolama için Azure Sentinel ' i "etkin" veriler için kullanmayı seçer.

- [Azure Izleyici ile platform günlükleri ve ölçümleri toplama](../azure-monitor/essentials/diagnostic-settings.md)

- [Azure Sentinel 'i ekleme](../sentinel/quickstart-onboard.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="lt-6-configure-log-storage-retention"></a>LT-6: Günlük depolama alanının saklama süresini yapılandırın

**Kılavuz**: Azure izleyici 'de, kuruluşunuzun uyumluluk düzenlemelerine göre Azure kaynaklarınızla ilişkili Log Analytics çalışma alanları için günlük tutma süresini ayarlayın.

- [Günlük tutma parametrelerini ayarlama](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="incident-response"></a>Olay Yanıtı

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Olay Yanıtı](/azure/security/benchmarks/security-controls-v2-incident-response).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: Hazırlık: Azure için olay yanıtı sürecini güncelleştirin

**Rehberlik**: Kuruluşunuzda güvenlik olaylarına yanıt vermeye yönelik süreçler bulunduğundan, bu süreçlerin Azure için güncelleştirildiğinden ve hazır olduğunuzdan emin olmak için bu süreçlerin düzenli olarak uygulandığından emin olun.

- [Güvenliği kuruluş genelinde uygulama](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Olay yanıtı başvuru kılavuzu](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: Hazırlık: Olay bildirimini ayarlayın

**Rehberlik**: Azure Güvenlik Merkezi'nde güvenlik olayı iletişim bilgilerini ayarlayın. Microsoft, Microsoft Güvenlik Yanıt Merkezi'nin (MSRC) verilerinize kanuna aykırı veya yetkisiz erişim sağlanmasını keşfetmesi durumunda sizinle iletişime geçmek için bu iletişim bilgilerini kullanır. İsterseniz farklı Azure hizmetlerindeki olay uyarılarını ve bildirimlerini olay yanıt gereksinimlerinize göre özelleştirebilirsiniz. 

- [Azure Güvenlik Merkezi güvenlik ilgili kişisini ayarlama](../security-center/security-center-provide-security-contact-details.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: algılama ve analiz – yüksek kaliteli uyarılara göre olaylar oluşturun 

**Rehberlik**: Yüksek kaliteli uyarılar oluşturmaya ve uyarıların kalitesini ölçmeye yönelik bir sürece sahip olduğunuzdan emin olun. Bu sayede önceki olaylardan dersler çıkarabilir ve analistler için uyarıları önceliklendirebilirsiniz. Bu sayede hatalı pozitif sonuçlarla zaman kaybetmelerini önlemiş olursunuz. 

Yüksek kaliteli uyarılar önceki olaylardan alınan dersler, doğrulanmış topluluk kaynakları ve farklı sinyal kaynaklarını birleştirip bağlantı oluşturarak uyarı oluşturmaya ve temizlemeye yönelik araçlar kullanılarak geliştirilebilir. 

Azure Güvenlik Merkezi, birçok Azure varlığı arasında yüksek kaliteli uyarılar sağlar. ASC veri bağlayıcısını kullanarak uyarıların Azure Sentinel’e akışını yapabilirsiniz. Azure Sentinel bir araştırmaya yönelik olayların otomatik olarak oluşturulması için gelişmiş uyarı kuralları oluşturur. 

Azure kaynaklarına yönelik riskleri tanımlamaya yardımcı olmak için, dışarı aktarma özelliğini kullanarak Azure Güvenlik Merkezi uyarılarınızı ve önerilerinizi dışarı aktarın. Uyarıları ve önerileri el ile veya sürekli bir biçimde dışarı aktarabilirsiniz.

- [Dışarı aktarma işlemini yapılandırma](../security-center/continuous-export.md)

- [Uyarıların Azure Sentinel’e akışını yapma](../sentinel/connect-azure-security-center.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: Algılama ve analiz: Olay araştırması

**Rehberlik**: analistlerin, ne olduğunu tam olarak görmek için olası olayları araştırıp farklı veri kaynaklarını sorgulayabileceği ve kullanabilmesi için analist emin olun. Sonlandırma zinciri boyunca olası saldırganın etkinliklerinin izlenebilmesi için çeşitli günlükler toplanmalı, kör noktaların olması önlenmelidir.  Ayrıca diğer analistler için ve gelecekte başvurmak üzere içgörülerin ve çıkarılan derslerin yakalandığından da emin olmalısınız.  

Araştırmaya yönelik veri kaynakları kapsam içindeki hizmetlerden ve çalışan sistemlerden zaten toplanmakta olan merkezi günlük kaynaklarının yanı sıra şunları da içerebilir:

- Ağ verileri – ağ akış günlüklerini ve diğer analiz bilgilerini yakalamak için ağ güvenlik gruplarının akış günlüklerini, Azure Ağ İzleyicisi’ni ve Azure İzleyici’ni kullanın. 

- Çalışan sistemlerin anlık görüntüleri: 

    - Çalışan sistemin diskinin anlık görüntüsünü oluşturmak için Azure sanal makinesinin anlık görüntü özelliğini kullanın. 

    - Çalışan sistemin belleğinin anlık görüntüsünü oluşturmak için işletim sisteminin yerel bellek dökümü özelliğini kullanın.

    - Çalışan sistemlerin anlık görüntülerini oluşturmak için Azure hizmetlerinin anlık görüntü özelliğini veya yazılımınızın kendi özelliğini kullanın.

Azure Sentinel hemen her veri kaynağı üzerinde kapsamlı veri analizi gerçekleştirdiği gibi, olayların tüm yaşam döngüsünü yöneten bir olay yönetim portalı da sağlar. Araştırma sırasında elde edilen inceleme bilgileri izleme ve raporlama amacıyla bir olayla ilişkilendirilebilir. 

- [Windows makinesi diskinin anlık görüntüsü](../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Linux makinesi diskinin anlık görüntüsü](../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Microsoft Azure Desteği tanılama bilgileri ve bellek dökümü toplama](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Azure Sentinel ile olayları araştırma](../sentinel/tutorial-investigate-cases.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: Algılama ve analiz: Olayları önceliklendirin

**Rehber**: Uyarı önem derecesi ve uyarı hassasiyeti temelinde analistlere önce hangi olaylara odaklanılacağı konusunda bağlam sağlayın. 

Azure Güvenlik Merkezi önce hangi uyarıların araştırılması gerektiğini belirlemenize yardımcı olmak için her uyarıya bir önem derecesi atar. Önem derecesi, güvenlik merkezi 'nin uyarıyı vermek için kullanılan bulma veya analizde ne kadar önemli olduğunu ve uyarıya yönelik etkinliğin arkasında kötü amaçlı bir amaç olduğunu bilmenin yanı sıra güven düzeyini temel alır.

Buna ek olarak Azure kaynaklarını, özellikle hassas verileri işleyen kaynakları belirlemek ve kategorilere ayırmak için etiketleri kullanarak kaynakları işaretleyin ve bir adlandırma sistemi oluşturun.  Azure kaynaklarının önem düzeyine ve olayın oluştuğu ortama bağlı olarak uyarıların çözümünde önceliği belirlemek sizin sorumluluğunuzdadır.

- [Azure Güvenlik Merkezi'nde güvenlik uyarıları](../security-center/security-center-alerts-overview.md)

- [Azure kaynaklarınızı düzenlemek için etiketleri kullanma](/azure/azure-resource-manager/resource-group-using-tags)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: Kapsama, ortadan kaldırma ve kurtarma: Olay işleme sürecini otomatikleştirin

**Rehberlik**: Yanıt süresini kısaltmak ve analistlerin yükünü hafifletmek için el ile yapılan yinelenen görevleri otomatikleştirin. El ile gerçekleştirilen görevlerin yürütülmesi daha uzun sürer ve olay yanıt sürecini yavaşlatarak analistlerin işleyebileceği olay sayısını azaltır. Ayrıca el ile gerçekleştirilen görevler analistler açısından daha yorucudur. Dolayısıyla gecikmelere neden olan insan hatası riskini artırır ve analistlerin karmaşık görevlere etkili bir şekilde odaklanma becerisini azaltır. Eylemleri otomatik olarak tetiklemek veya gelen güvenlik uyarılarını yanıtlamak üzere bir playbook çalıştırmak için Azure Güvenlik Merkezi’nin ve Azure Sentinel’in iş akışı otomasyonu özelliklerini kullanın. Playbook bildirimleri gönderme, hesapları devre dışı bırakma ve sorunlu ağları yalıtma gibi eylemleri gerçekleştirir. 

- [Güvenlik Merkezi’nde iş akışı otomasyonunu yapılandırma](../security-center/workflow-automation.md)

- [Azure Güvenlik Merkezi’nde otomatik tehdit yanıtlarını ayarlama](https://docs.microsoft.com/azure/security-center/tutorial-security-incident#triage-security-alerts)

- [Azure Sentinel’de otomatik tehdit yanıtlarını ayarlama](../sentinel/tutorial-respond-threats-playbook.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="posture-and-vulnerability-management"></a>Duruş ve Güvenlik Açığı Yönetimi

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Duruş ve Güvenlik Açığı Yönetimi](/azure/security/benchmarks/security-controls-v2-vulnerability-management).*

### <a name="pv-1-establish-secure-configurations-for-azure-services"></a>PV-1: Azure hizmetleri için güvenli yapılandırmalar oluşturun 

**Rehberlik**: kullandıkları Azure hizmetlerini güvenli bir şekilde yapılandırmayı kolaylaştırarak altyapı ve DevOps ekipleri için güvenlik engellerini tanımlayın. 

Azure Güvenlik kıyaslaması içindeki hizmet temellerine sahip Azure Hizmetleri Güvenlik yapılandırmanızı başlatın ve kuruluşunuz için gereken şekilde özelleştirin. 

Azure Güvenlik Merkezi 'ni kullanarak Azure kaynaklarınızın yapılandırmasını denetleme ve zorunlu kılmak üzere Azure Ilkesi 'ni yapılandırın. 

Tek bir şema tanımında Azure Resource Manager şablonları, Azure RBAC denetimleri ve ilkeleri de dahil olmak üzere hizmetlerin ve uygulama ortamlarının dağıtımını ve yapılandırmasını otomatik hale getirmek için Azure şemaları ' nı kullanabilirsiniz.

- [Kurumsal ölçekte giriş bölgesindeki guardrayın uygulamasının çizimi](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture#landing-zone-expanded-definition)

- [Azure Güvenlik Merkezi 'nde güvenlik ilkeleriyle çalışma](../security-center/tutorial-security-policy.md)

- [Uyumluluğu zorunlu tutmak için ilkeleri oluşturma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Azure Blueprints](../governance/blueprints/overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="pv-2-sustain-secure-configurations-for-azure-services"></a>PV-2: Azure hizmetleri için güvenli yapılandırmaların sürekliliğini sağlayın

**Rehberlik**: uygulanamaz;  Bu öneri, işlem kaynaklarına yöneliktir.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: Düzenli aralıklarla saldırı simülasyonları yapın

**Rehberlik**: Gerektiğinde Azure kaynaklarınızda sızma testi veya kırmızı takım etkinlikleri gerçekleştirerek tüm kritik güvenlik bulgularının düzeltilmesini sağlayın.
Sızma testlerinizin Microsoft ilkelerini ihlal etmediğinden emin olmak için Microsoft Bulut Sızma Testi Etkileşim Kuralları'na uygun hareket edin. Microsoft tarafından yönetilen bulut altyapısına, hizmetlere ve uygulamalara yönelik kırmızı takım ve canlı site sızma testi gerçekleştirmek için Microsoft'un stratejisini ve yürütme sürecini kullanın.

- [Azure'da sızma testi yapma](../security/fundamentals/pen-testing.md)

- [Sızma Testi Etkileşim Kuralları](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Bulut ile Kırmızı Takım Oluşturma](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: yok

## <a name="governance-and-strategy"></a>İdare ve Strateji

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: İdare ve Strateji](/azure/security/benchmarks/security-controls-v2-governance-strategy).*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: Varlık yönetimi ve veri koruma stratejisi tanımlayın 

**Rehberlik**: Sistemlerin ve verilerin sürekli izlenmesine ve korunmasına yönelik net bir strateji belgelendirdiğinizden ve paylaştığınızdan emin olun. İş açısından kritik verilerin ve sistemlerin bulma, değerlendirme, koruma ve izleme süreçleri için öncelik belirleyin. 

Bu strateji kapsamında aşağıdaki öğeler için rehberlik, ilkeler ve standartlar belgelenmiş olmalıdır: 

-   İş risklerine göre veri sınıflandırma standardı

-   Riskler ve varlık envanteriyle ilgili güvenlik kuruluşu görünürlüğü 

-   Güvenlik kuruluşunun kullanılmasını onayladığı Azure hizmetleri 

-   Varlıkların yaşam döngüsü boyunca güvenliği

-   Kuruluşun veri sınıflandırmasına uygun gerekli erişim denetimi stratejisi

-   Yerel Azure ve üçüncü taraf veri koruma özelliklerinin kullanılması

-   Taşıma durumundaki ve bekleyen veriler için şifreleme gereksinimleri

-   Uygun şifreleme standartları

Daha fazla bilgi için aşağıdaki başvuruları inceleyin:
- [Azure Güvenlik Mimarisi Önerileri: Depolama, veri ve şifreleme](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json)

- [Azure Güvenliği Temelleri: Azure'da veri güvenliği, şifreleme ve depolama](../security/fundamentals/encryption-overview.md)

- [Bulut Benimseme Çerçevesi: Azure'da veri güvenliği ve şifreleme için en iyi deneyimler](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices?toc=/azure/cloud-adoption-framework/toc.json&amp;bc=/azure/cloud-adoption-framework/_bread/toc.json)

- [Azure Güvenlik Karşılaştırması: Varlık yönetimi](/azure/security/benchmarks/security-benchmark-v2-asset-management)

- [Azure Güvenlik Karşılaştırması: Veri koruma](/azure/security/benchmarks/security-benchmark-v2-data-protection)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: Kurumsal segmentasyon stratejisini tanımlayın 

**Rehberlik**: Kimlik, ağ, uygulama, abonelik, yönetim grubu ve diğer denetimleri kullanarak varlık erişimi segmentasyonuna yönelik kuruluş genelinde kullanılacak bir strateji belirleyin.

Güvenlik ayrımı gereksinimiyle birbirleriyle iletişim kurma ve verilere erişme gereksinimine sahip olan sistemlerin günlük çalışmasını etkinleştirme gereksinimi arasında bir denge kurun.

Segmentasyon stratejisinin ağ güvenliği, kimlik ve erişim modelleri, uygulama izinleri/erişim modelleri ve insanlar tarafından gerçekleştirilen süreç denetimleri gibi farklı denetim türlerinde tutarlı bir şekilde uygulandığından emin olun.

- [Azure için segmentasyon stratejisi rehberi (video)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Azure için segmentasyon stratejisi rehberi (belge)](/security/compass/governance#enterprise-segmentation-strategy)

- [Ağ segmentasyonunu kurumsal segmentasyon stratejisiyle uyumlu hale getirme](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: Güvenlik duruşu yönetim stratejisini tanımlayın

**Rehberlik**: Varlıklarınız ve içinde bulundukları ortamla ilgili riskleri sürekli olarak ölçün ve ortadan kaldırın. Yayımlanmış uygulamalar, ağ giriş ve çıkış noktaları, kullanıcı ve yönetici uç noktaları gibi değeri yüksek varlıkları ve kullanıma açık olan saldırı yüzeylerini önceliklendirin.

- [Azure Güvenlik Karşılaştırması: Duruş ve güvenlik açığı yönetimi](/azure/security/benchmarks/security-benchmark-v2-posture-vulnerability-management)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: Kuruluş genelindeki rolleri, sorumlulukları ve hesap verilebilirlik durumlarını uyumlu hale getirin

**Rehberlik**: Güvenlik kuruluşunuzdaki roller ve sorumluluklar için net bir strateji oluşturup bunu belgelendirdiğinizden ve paylaştığınızdan emin olun. Güvenlik kararları için net bir hesap verilebilirlik süreci oluşturmayı önceliklendirin, herkesi paylaşılan sorumluluk modeli hakkında eğitin ve teknik ekiplere bulut ortamının güvenliğini sağlamaya yönelik eğitimler verin.

- [Azure Güvenliği En İyi Deneyimi 1 - İnsanlar: Ekipleri Bulut Güvenliği Yolculuğu Hakkında Eğitin](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Azure Güvenliği En İyi Deneyimi 2 - İnsanlar: Ekipleri Bulut Güvenliği Teknolojileri Hakkında Eğitin](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Azure Güvenliği En İyi Deneyimi 3 - Süreç: Bulut Güvenliği Kararları için Hesap Verilebilirlik Ataması Yapın](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="gs-5-define-network-security-strategy"></a>GS-5: Ağ güvenlik stratejisini tanımlayın

**Rehberlik**: Kuruluşunuzun genel güvenlik erişimi denetimi stratejisi kapsamında Azure ağ güvenliği için bir yaklaşım belirleyin.  

Bu strateji kapsamında aşağıdaki öğeler için rehberlik, ilkeler ve standartlar belgelenmiş olmalıdır: 

-   Merkezi ağ yönetimi ve güvenlik sorumluluğu

-   Kurumsal segmentasyon stratejisiyle uyumlu sanal ağ segmentasyon modeli

-   Farklı tehdit ve saldırı senaryolarına yönelik düzeltme stratejisi

-   İnternet uç düğümü ile giriş ve çıkış stratejisi

-   Hibrit bulut ve şirket içi bağlantı stratejisi

-   Güncel ağ güvenliği yapıtları (ağ diyagramları, başvuru amaçlı ağ mimarisi vb.)

Daha fazla bilgi için aşağıdaki başvuruları inceleyin:
- [Azure Güvenliği En İyi Deneyimi 11 - Mimari. Tek ve birleşik güvenlik stratejisi](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure Güvenlik Karşılaştırması: Ağ Güvenliği](/azure/security/benchmarks/security-benchmark-v2-network-security)

- [Azure ile ağ güvenliğine genel bakış](../security/fundamentals/network-overview.md)

- [Kurumsal ağ mimarisi stratejisi](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: Kimlik ve ayrıcalıklı erişim stratejisini tanımlayın

**Rehberlik**: Kuruluşunuzun genel güvenlik erişimi denetimi stratejisi kapsamında Azure kimlik ve ayrıcalıklı erişim yaklaşımları belirleyin.  

Bu strateji kapsamında aşağıdaki öğeler için rehberlik, ilkeler ve standartlar belgelenmiş olmalıdır: 

-   Merkezi bir kimlik ve kimlik doğrulaması sistemi ile diğer iç ve dış kimlik sistemleriyle olan bağlantısı

-   Farklı kullanım örneklerine ve koşullara yönelik güçlü kimlik doğrulama yöntemleri

-   Yüksek ayrıcalıklı kullanıcıların korunması

-   Kullanıcı etkinlikleri için anomali izleme ve işleme  

-   Kullanıcı kimliği ve erişim gözden geçirmesi ile uzlaştırma süreci

Daha fazla bilgi için aşağıdaki başvuruları inceleyin:

- [Azure Güvenlik Karşılaştırması: Kimlik yönetimi](/azure/security/benchmarks/security-benchmark-v2-identity-management)

- [Azure Güvenlik Karşılaştırması: Ayrıcalıklı erişim](/azure/security/benchmarks/security-benchmark-v2-privileged-access)

- [Azure Güvenliği En İyi Deneyimi 11 - Mimari. Tek ve birleşik güvenlik stratejisi](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure'da kimlik yönetim güvenliğine genel bakış](../security/fundamentals/identity-management-overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: Günlüğe kaydetme ve tehdit yanıtı stratejisi tanımlayın

**Rehberlik**: Uyumluluk gereksinimleri kapsamında tehditleri hızlı bir şekilde algılamak ve düzeltmek için bir günlüğe kaydetme ve tehdit yanıtı stratejisi oluşturun. Tümleştirme ve el ile gerçekleştirilen adımlar yerine tehditlere odaklanabilmeleri için analistlere yüksek kaliteli uyarılar ve sorunsuz deneyimler sunmayı önceliklendirin. 

Bu strateji kapsamında aşağıdaki öğeler için rehberlik, ilkeler ve standartlar belgelenmiş olmalıdır: 

-   Güvenlik operasyonları (SecOps) organizasyonunun rolü ve sorumlulukları 

-   NIST veya diğer sektör çerçeveleriyle uyumlu iyi tanımlanmış bir olay yanıt süreci 

-   Tehdit algılama, olay yanıtı ve uyumluluk gereksinimlerini karşılamak için günlük yakalama ve saklama

-   SIEM, yerel Azure özellikleri ve diğer kaynaklar aracılığıyla merkezi görünürlük ve bilgi bağıntısı 

-   Planı müşterilerinize, tedarikçilerinize ve diğer proje katılımcılarına iletme ve onları bilgilendirme

-   Günlüğe kaydetme ve tehdit algılama, adli araştırma, saldırı önleme ve ortadan kaldırma gibi olay işleme adımları için yerel Azure ve üçüncü taraf platformları kullanma

-   Olayları ve çıkarılan dersler ile kanıt koruma gibi olay sonrası etkinlikleri işlemeye yönelik süreçler

Daha fazla bilgi için aşağıdaki başvuruları inceleyin:

- [Azure Güvenlik Karşılaştırması: Günlüğe kaydetme ve tehdit algılama](/azure/security/benchmarks/security-benchmark-v2-logging-threat-detection)

- [Azure Güvenlik Karşılaştırması: Olay yanıtı](/azure/security/benchmarks/security-benchmark-v2-incident-response)

- [Azure Güvenliği En İyi Deneyimi 4 - Süreç. Bulut için Olay Yanıt Sürecini Güncelleştirme](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Azure Benimseme Çerçevesi, günlüğe kaydetme ve raporlama kararı rehberi](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Azure ile kurumsal ölçek, yönetim ve izleme](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="next-steps"></a>Sonraki adımlar

- Bkz. [Azure Güvenlik Karşılaştırması 2.0 sürümüne genel bakış](/azure/security/benchmarks/overview)
- [Azure güvenlik temelleri](/azure/security/benchmarks/security-baselines-overview) hakkında daha fazla bilgi edinin
