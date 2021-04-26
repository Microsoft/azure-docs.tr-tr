---
title: Azure Active Directory için genel uygulama yönetimi senaryoları | Microsoft Docs
description: Azure AD ile uygulama yönetimini merkezileştirme
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: overview
ms.date: 03/02/2019
ms.author: iangithinji
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1acfff8a9152c767b61463bed0165d5ae390f649
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107375007"
---
# <a name="centralize-application-management-with-azure-ad"></a>Azure AD ile uygulama yönetimini merkezileştirme

Parolalar, hem It nightmhem de dünyanın her yerindeki çalışanlar için bir sorun. Bunun nedeni, Microsoft 'un bulut ve diğer tüm kaynaklarınız için kimlik ve erişim yönetimi çözümü olan Azure Active Directory daha fazla şirket, Microsoft 'un kimlik ve erişim yönetimi çözümünü de kapatıyor. Her biri için bir parola girmek zorunda kalmadan uygulamadan uygulamaya atlayın. Outlook 'tan Workday 'e, hızlı ve güvenli bir şekilde açabileceğiniz en hızlı şekilde ADP 'ye atlayın. Daha sonra iş ortaklarıyla işbirliği yapmak zorunda kalmadan iş ortakları ve hatta diğer kullanıcılarla işbirliği yapın. Azure AD, ne olursa olsun, ihtiyacınız olan uygulamalara güvenli erişim sağlayan şüpheli oturum açma işlemlerini tespit etmek için sürekli Uyarlamalı makine öğrenimi ve güvenlik zekasını kullanarak, sizin tarafınızdan nerede olduğunu doğrulamak üzere çok faktörlü kimlik doğrulaması gibi şeyler ile, kullandığınız uygulamaların güvenliğini sağlamaya yardımcı olur. Yalnızca kullanıcılar için de harika değildir. Tam zamanında erişim İncelemeleri ve tam ölçekli bir idare paketi sayesinde Azure AD, uyumluluk ve ilkeleri zorunlu tutmanıza yardımcı olur. Bunun yanı sıra, sağlama Kullanıcı hesapları da otomatikleştirerek, erişim yönetiminin bir Breeze olmasını sağlayabilirsiniz. müşterinin için Azure Active Directory Uygulama yönetimi özelliklerini kullanmasının bazı yaygın senaryolarına göz atın.

**Genel senaryolar**


> [!div class="checklist"]
> * Tüm uygulamalarınız için SSO
> * Sağlamayı otomatik hale getirme ve sağlamayı kaldırma 
> * Uygulamalarınızın güvenliğini sağlama
> * Uygulamalarınıza erişimi yönetir
> * Karma güvenli erişim

## <a name="scenario-1-set-up-sso-for-all-your-applications"></a>Senaryo 1: tüm uygulamalarınız için SSO 'yu ayarlama

Daha fazla parola yönetilme. Şirket kimlik bilgilerinizle ihtiyacınız olan tüm kaynaklara güvenli bir şekilde erişin. 

|Özellik  | Açıklama | Öneri |
|---------|---------|---------|
|SSO|Güvenilen sektör standartları kullanarak standartlara dayalı Federasyon SSO 'SU.|Uygulamanız destekliyorsa, SSO 'yu etkinleştirmek için her zaman [SAML/OıDC](../develop/v2-howto-app-gallery-listing.md) kullanın.|
|Uygulamalarım|Kullanıcılarınıza tüm uygulamalarını keşfetmek ve bunlara erişmek için basit bir hub sunun. Uygulamalar ve gruplar için erişim isteğinde bulunma veya diğerleri adına kaynaklara erişimi yönetme gibi self servis özellikleri ile daha üretken olmalarını sağlayın.| Uygulamalarınızı SSO için Azure AD ile tümleştirdikten sonra [uygulamalarımı](my-apps-deployment-plan.md) kuruluşunuzda dağıtın.|

## <a name="scenario-2-automate-provisioning-and-deprovisioning"></a>Senaryo 2: sağlamayı ve sağlamayı kaldırmayı otomatikleştirme 


Çoğu uygulama, ihtiyaç duydukları kaynaklara erişmeden önce bir kullanıcının uygulamaya sağlanması gerekir. CSV dosyalarını veya karmaşık betikleri kullanmak maliyetli ve yönetimi zor olabilir. Ayrıca, müşterilerin artık erişimleri olmaması durumunda hesapların kaldırılmasını sağlamak gerekir. Sağlama ve sağlamayı kaldırma işlemlerini otomatikleştirmek için aşağıdaki araçlardan yararlanın. 


|Özellik  |Açıklama|Öneri |
|---------|---------|---------|
|SCıM sağlama|[SCIM](https://aka.ms/SCIMOverview) , Kullanıcı sağlamasını otomatikleştirmek için sektör açısından en iyi uygulamadır. Herhangi bir SCıM uyumlu uygulama, Azure AD ile tümleştirilebilir. CSV dosyalarını, özel betikleri veya şirket içi çözümleri sürdürmek zorunda kalmadan kullanıcı hesaplarını otomatik olarak oluşturun, güncelleştirin ve silin.|Azure AD uygulama galerisinde [önceden tümleştirilmiş](../saas-apps/tutorial-list.md) uygulamalar listesini gözden geçirin|
|Microsoft Graph|Azure AD 'nin uygulamanızı, ihtiyaç duyacağı verilerle zenginleştirmesini sağlayan verilerin genişliği ve derinliğine yararlanın.|Microsoft ekosistemi üzerinden veri almak için [Microsoft Graph](https://developer.microsoft.com/graph/) 'tan yararlanın. |


## <a name="scenario-3-secure-your-applications"></a>Senaryo 3: uygulamalarınızın güvenliğini sağlama
Kimlik, güvenlik için linchpın 'dir. Bir kimlik tehlikeye atılırsa, çok geç olmadan önce Domino efektini durdurmak inanılmaz zordur. Kuruluşların bir güvenliğinin aşılmasına neden olduğunu fark etmeden önce 100 gün üzerinden geçen süre. Uygulamalarınızın güvenlik duruşunu geliştirmek için Azure AD tarafından sunulan araçları kullanın. 

|Özellik  |Açıklama| Öneri |
|---------|---------| ---------|
|Azure AD MFA|Azure AD Multi-Factor Authentication (MFA), Microsoft 'un iki adımlı doğrulama çözümüdür. Yönetici onaylı kimlik doğrulama yöntemlerini kullanarak, Azure AD MFA, basit bir oturum açma işlemi talebini karşılarken verilerinize ve uygulamalarınıza erişimi korumaya yardımcı olur.| Kullanıcılarınız için [MFA 'Yı etkinleştirin](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/all-your-creds-are-belong-to-us/ba-p/855124) .  |
|Koşullu Erişim|Koşullu erişimle, koşullara göre bulut uygulamalarınıza kimlerin erişebileceği otomatik erişim denetimi kararları uygulayabilirsiniz.| Müşterilerin kullandığı [güvenlik varsayılanlarını](../fundamentals/concept-fundamentals-security-defaults.md) ve [ortak ilkeleri](../conditional-access/concept-conditional-access-policy-common.md) gözden geçirin. | 
|Kimlik Koruması|Kimlik koruması, Microsoft 'un kuruluşların konumlarından Azure AD, Microsoft hesaplarıyla tüketici alanı ve Xbox ile oyun oynarken kullanıcılarınızı koruduğu dersleri kullanır. Microsoft analizler 6.500.000.000.000 sinyalleri, müşterileri tespit etmek ve tehditlere karşı korumak için günde.|Hizmetimizin tarafından sunulan [varsayılan kimlik koruması ilkelerini](../identity-protection/concept-identity-protection-policies.md) etkinleştirin. | 

## <a name="scenario-4-govern-access-to-your-applications"></a>Senaryo 4: uygulamalarınıza erişimi yönetir
Kimlik yönetimi, kuruluşların üretkenlik arasında bir denge elde etmesine yardımcı olur ve kuruluşa ne zaman katılabilecekleri gibi bir kişinin ihtiyacı olan uygulamalara ne kadar hızlı erişmesini sağlayabilir? Ve güvenlik-bu kişinin iş durumundaki değişiklikler nedeniyle, zaman içinde erişimin ne zaman içinde değiştirilmesi gerekir? 

|Özellik  |Açıklama|Öneri |
|---------|---------| ---------|
|KARA|Azure AD Yetkilendirme Yönetimi, kuruluşunuzun içindeki ve dışındaki kullanıcıların uygulamalarına erişimi daha verimli bir şekilde yönetmesine yardımcı olabilir.| Yönetici olmayan uygulamalara erişim [paketleriyle](../governance/entitlement-management-access-package-first.md)erişimi yönetmesine izin verin.|
|Erişim Gözden Geçirmeleri|Kullanıcıların uygulamalara erişimi, yalnızca doğru kişilerin erişmeye devam ettiğinden emin olmak için düzenli olarak incelenebilir.| En hassas uygulamalarınıza [erişimi gözden geçirin](../governance/access-reviews-overview.md) . |
|Log Analytics|Hangi uygulamalara kimin eriştiğini ve verileri veri kaynakları arasında ve zaman içindeki verileri ilişkilendirmek için onu tercih ettiğiniz SıEM aracında depolayan raporlar oluşturun.| [Log Analytics](../reports-monitoring/howto-analyze-activity-logs-log-analytics.md) 'i etkinleştirin ve uygulamalarınızla ilgili kritik olaylar için Uyarılar ayarlayın. |


## <a name="scenario-5-hybrid-secure-access"></a>Senaryo 5: karma güvenli erişim
Kimlik yalnızca bulut ve şirket içi uygulamalardaki her şeyi bağlayabildiği durumlarda denetim düzledir. Eski kimlik doğrulama tabanlı uygulamalara güvenli erişim sağlamak için Azure AD ve iş ortakları tarafından sunulan araçlardan yararlanın.

|Özellik  |Açıklama|Öneri |
|---------|---------|---------|
|Uygulama Ara Sunucusu|Günümüzde çalışanlar her yerden, her zaman ve tüm cihazlardan çalışmak istemektedir. Bulut ve şirket içi uygulamalarda SaaS uygulamalarına erişmesi gerekir. Azure AD uygulama proxy 'si, pahalı ve karmaşık sanal özel ağlar (VPN) veya sivil bölgeler (DMZs) olmadan bu güçlü erişimi mümkün hale getirmenizi sunar.|Şirket içi uygulamalarınız için [Uzaktan erişim](./application-proxy.md) ayarlayın. |
|F5, Akamai, Zscaler|Mevcut ağ ve teslim denetleyicinizi kullanarak, İş süreçleriniz için hala kritik olan eski uygulamaları kolayca koruyabilir ancak Azure AD ile önce koruyamazsınız. Bu uygulamaları korumaya başlamak için ihtiyacınız olan her şeye zaten sahipsiniz.| Akamai, Citrix, F5 veya Zscaler kullanılıyor mu? [Önceden oluşturulmuş çözümlerimize](./secure-hybrid-access.md)göz atın. | 

## <a name="related-articles"></a>İlgili makaleler:

- [Uygulama yönetimi](./index.yml)
- [Uygulama sağlama](../app-provisioning/user-provisioning.md)
- [Karma güvenli erişim](./secure-hybrid-access.md)
- [Kimlik idaresi](../governance/identity-governance-overview.md)
- [Microsoft kimlik platformu](../develop/v2-overview.md)
- [Kimlik güvenliği](../conditional-access/index.yml)