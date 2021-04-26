---
title: Azure Active Directory ile dış işbirliği için güvenlik durunuzu belirleme
description: Bir dış erişim güvenlik planını yürütebilmeniz için önce ne elde etmek istediğinizi belirlemelisiniz.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 37c27e84f15a01a2d8832baae137518685de59a8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98725450"
---
# <a name="determine-your-security-posture-for-external-access"></a>Dış erişim için güvenlik durunuzu belirleme 

Dış erişimi yönetirken siz de kuruluşunuzun her bir senaryosunda güvenlik ve işbirliği gereksinimlerini değerlendirmeniz gerekir. Kuruluş düzeyinde, BT ekibinizin günlük işbirliği üzerinde sahip olması için ihtiyaç duyduğunuz denetim miktarını göz önünde bulundurun. Düzenlenen sektörlerdeki kuruluşlar, daha fazla BT denetimi gerektirebilir. Örneğin, bir savunma yüklenicisi her bir dış kullanıcıyı, erişimleri ve erişimin kaldırılmasını olumlu olarak tanımlamak ve belgelemek için gerekli olabilir. Bu gereksinim, tüm erişimlerinde veya belirli senaryolarda veya iş yükleri üzerinde olabilir. Daha önce, bir danışmanlık firması, son kullanıcıların, birlikte işbirliği yapmaları gereken dış kullanıcıları, belirli BT koruyucu rayları dahilinde belirlemesine izin verebilir. 

![BT, son kullanıcı işbirliği denetimine karşı](media/secure-external-access/1-overall-control.png)

> [!NOTE]
> İşbirliğinde daha sıkı denetim, BT bütçeleri, azaltılan verimlilik ve Gecikmeli iş sonuçları ile aynı olabilir. Resmi işbirliği kanalları çok onerou olarak algılandıklarında, son kullanıcılar BT 'nin işlerini yapmak için sistem tarafından sağlanarak güvenli olmayan belgelere e-posta göndererek bu işlemleri halledebilir.

## <a name="think-in-terms-of-scenarios"></a>Senaryolar açısından düşünün

Çoğu durumda, güvenlik için koruma raylarını sağlarken, en azından bazı senaryolarda iş ortağı erişimi temsilciliğini alabilir. BT koruyucusu, fikri mülkiyet güvenliğinin güvende kaldığından emin olmanıza yardımcı olmakla birlikte çalışanların işi yapması için iş ortaklarıyla işbirliği yapmasını güçlendirin.

Kuruluşunuzdaki senaryoları göz önünde bulundursanız, çalışan iş ortağı ve kaynaklara erişimi gereksinimini değerlendirin. Bir banka, Kullanıcı hesabı bilgileri gibi belirli kaynaklara erişimi bir dahili çalışanların küçük grubuna kısıtlayan uyumluluk ihtiyaçlarına sahip olabilir. Buna karşılık, aynı banka, bir pazarlama kampanyasında çalışan iş ortakları için temsilci erişimi de sağlayabilir.

![Senaryo başına idare eden Continuum](media\secure-external-access\1-scenarios.png)

Her senaryoya göre şunları göz önünde bulundurun 

* risk altındaki bilgilerin duyarlılığı

* diğer kullanıcılarla ilgili iş ortaklarının neleri görebileceğini kısıtlayıp kısıtlamadığınız

* bir ihlalin maliyeti merkezi denetim ve Son Kullanıcı uyuşmazlığı 'nın ağırlığı ile

 Ayrıca, uyumluluk hedeflerini karşılamak ve zaman içinde son kullanıcılara denetim sağlamak için merkezi olarak yönetilen denetimlerle de başlayabilirsiniz. Tüm erişim yönetimi modelleri bir kuruluşta aynı anda bir arada bulunabilir. 

[İş ortağı tarafından yönetilen kimlik bilgileri](../external-identities/what-is-b2b.md) kullanımı, dış Kullanıcı kendi şirketinin kaynaklarına erişimi kaybettikten sonra kaynaklarınıza erişimi sonlandıran önemli bir sinyal sağlar.

## <a name="goals-of-securing-external-access"></a>Dış erişimin güvenliğini sağlama amaçları

BT tarafından yönetilen ve temsilci erişimi amaçları farklıdır.

**BT tarafından yönetilen bu erişimin birincil amaçları şunlardır:**

* İdare, mevzuat ve uyumluluk (GRC) hedeflerini karşılayın. 

* Daha sıkı denetim iş ortağı erişimi ve üye kullanıcıları, grupları ve diğer iş ortakları hakkında iş ortakları neler görebilir.

**Erişim yetkisi verme birincil amaçları şunlardır:**

* BT kısıtlamalarında iş sahiplerini, ile birlikte çalıştıkları kişileri yönetecek şekilde etkinleştirin.

* İş ortaklarının, iş sahipleri tarafından tanımlanan kurallara göre erişim istemesine olanak tanır.

Kuruluşunuz ve senaryolarınız için işlem yaptığınızda şunlar gerekir: 

* **Uygulamalara, verilere ve içeriğe erişimi denetleme**. Bu, [Azure AD](https://azure.microsoft.com/pricing/details/active-directory/) ve [Microsoft 365](https://www.microsoft.com/microsoft-365/compare-microsoft-365-enterprise-plans)sürümlerinize bağlı olarak çeşitli yöntemlerle gerçekleştirilebilir. 

* **Saldırı yüzeyini küçültün**. [Ayrıcalıklı kimlik yönetimi](../privileged-identity-management/pim-configure.md), [veri kaybı önleme (DLP)](/exchange/security-and-compliance/data-loss-prevention/data-loss-prevention) ve [şifreleme özellikleri](/exchange/security-and-compliance/data-loss-prevention/data-loss-prevention) saldırı yüzeyini azaltır.

* **Uyumluluğu onaylamak Için düzenli olarak etkinlik ve denetim günlüğü gözden geçirin**. Erişim gözden geçirmeleri, sürekli erişimi düzenli olarak onaylamaya yönelik bir yol sağlarken, yetkilendirme yönetimi aracılığıyla iş sahiplerine erişim kararları devredebilir. Duyarlılık etiketleriyle otomatik veri sınıflandırması, çalışan son kullanıcılarının uyum sağlaması için hassas içerik şifrelemesini otomatik hale getirmeye yardımcı olur.

## <a name="next-steps"></a>Sonraki adımlar 

Kaynaklara dışarıdan erişimi güvenli hale getirmek için aşağıdaki makalelere bakın. Eylemleri listelenen sırayla yapmanızı öneririz.

1. [Dış erişim için güvenlik durunuzu saptayın](1-secure-access-posture.md) (burada bulabilirsiniz.)

2. [Geçerli durumlarınızı bulma](2-secure-access-current-state.md)

3. [İdare planı oluşturma](3-secure-access-plan.md)

4. [Güvenlik için grupları kullanma](4-secure-access-groups.md)

5. [Azure AD B2B 'ye geçiş](5-secure-access-b2b.md)

6. [Yetkilendirme yönetimiyle güvenli erişim](6-secure-access-entitlement-managment.md)

7. [Koşullu erişim ilkeleriyle güvenli erişim](7-secure-access-conditional-access.md)

8. [Duyarlılık etiketleriyle güvenli erişim](8-secure-access-sensitivity-labels.md)

9. [Microsoft ekipleri, OneDrive ve SharePoint 'e güvenli erişim](9-secure-access-teams-sharepoint.md)
 

