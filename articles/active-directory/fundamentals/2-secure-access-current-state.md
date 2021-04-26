---
title: Azure Active Directory dış işbirliğinin geçerli durumunu bulma
description: İşbirliğinin geçerli durumunu bulma yöntemlerini öğrenin.
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
ms.openlocfilehash: 30858e9978f7e8857c5f8a2dcdfd7455f6e97b60
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102553434"
---
# <a name="discover-the-current-state-of-external-collaboration-in-your-organization"></a>Kuruluşunuzda dış işbirliğinin geçerli durumunu bulma 

Dış işbirliğinin geçerli durumunu bulmadan önce, [istediğiniz güvenlik duruşunuzu belirlemelisiniz](1-secure-access-posture.md). Kuruluşunuzun merkezi ve Temsilcili denetim için gereksinimlerini ve ilgili idare, mevzuat ve uyumluluk hedeflerini kabul edersiniz. 

Kuruluşunuzdaki kişiler büyük olasılıkla diğer kuruluşlardaki kullanıcılarla işbirliği yaparken. İşbirliği, Microsoft 365 gibi üretkenlik uygulamalarında, e-postayla veya harici kullanıcılarla kaynakları paylaşarak özellikler aracılığıyla olabilir. İdare planınızın, keşfettiğiniz şekilde biçimi görüntülenir 
*   dış işbirliği başlatan kullanıcılar.
*   işbirliği yaptığınız dış kullanıcılar ve kuruluşlar.
*   dış kullanıcılara verilen erişim.


## <a name="users-initiating-external-collaboration"></a>Dış işbirliği başlatan kullanıcılar

Dış işbirliği başlatan kullanıcılar, dış işbirliği için en uygun uygulamaları ve erişimin ne zaman bitmesi gerektiğini en iyi şekilde anlayın. Bu kullanıcıları anlamak, dış kullanıcıları davet etme, erişim paketleri oluşturma ve erişim incelemelerini tamamlamaya yönelik hangi kişilere temsilci atanmış olduğunu belirlemenize yardımcı olabilir.

Şu anda işbirliği yapan kullanıcıları bulmak için, [Paylaşım ve erişim isteği etkinliklerinin Microsoft 365 denetim günlüğünü](/microsoft-365/compliance/search-the-audit-log-in-security-and-compliance#sharing-and-access-request-activities)gözden geçirin. Ayrıca, [Azure AD denetim günlüğünü, B2B kullanıcılarını dizininize davet eden Ayrıntılar için](../external-identities/auditing-and-reporting.md) de gözden geçirebilirsiniz.

## <a name="find-current-collaboration-partners"></a>Geçerli işbirliği iş ortaklarını bulun

Dış kullanıcılar, [Azure AD B2B kullanıcıları](../external-identities/what-is-b2b.md) (tercih edilen) iş ortağı tarafından yönetilen kimlik bilgileriyle veya yerel olarak sağlanan kimlik bilgilerine sahip harici kullanıcılarla olabilir. Bu kullanıcılar genellikle UserType Konukiyle işaretlenir (ancak her zaman değildir). Konuk kullanıcıları [MICROSOFT Graph API](/graph/api/user-list?tabs=http), [PowerShell](/graph/api/user-list?tabs=http)veya [Azure Portal](../enterprise-users/users-bulk-download.md)üzerinden sıralayabilirsiniz.

### <a name="use-email-domains-and-companyname-property"></a>E-posta etki alanlarını ve companyName özelliğini kullanma

Dış kuruluşlar, dış kullanıcı e-posta adreslerinin etki alanı adlarıyla belirlenebilir. Google gibi tüketici kimlik sağlayıcıları destekleniyorsa, bu mümkün olmayabilir. Bu durumda, kullanıcının dış organizasyonunu açıkça tanımlamak için companyName özniteliğini yazmanızı öneririz.

### <a name="use-allow-or-deny-lists"></a>İzin verme veya reddetme listelerini kullan

Kuruluşunuzun yalnızca belirli kuruluşlara işbirliğine izin vermek isteyip istemediğini göz önünde bulundurun. Alternatif olarak, kuruluşunuzun belirli kuruluşlara işbirliğini engellemek isteyip istemediğini göz önünde bulundurun.  Kiracı düzeyinde, kaynak (örn. takımlar, SharePoint ve Azure Portal) ne olursa olsun, genel B2B davetlerini ve kampanyaya denetlemek için kullanılabilen bir [izin verme veya reddetme listesi](../external-identities/allow-deny-list.md)bulunur.
Yetkilendirme Yönetimi kullanıyorsanız, aşağıda gösterildiği gibi belirli bağlı kuruluşlar ayarını kullanarak, erişim paketlerini iş ortaklarınızın bir alt kümesine de atayabilirsiniz.


![Yeni bir erişim paketi oluştururken reddetme listesine izin ver ekranının ekran görüntüsü.](media/secure-external-access/2-new-access-package.png)


## <a name="find-access-being-granted-to-external-users"></a>Dış kullanıcılara verilen erişim bulma

Dış Kullanıcı ve kuruluşların envanterini aldıktan sonra, Azure AD [Grup üyeliğini](/graph/api/resources/groups-overview) veya Azure AD 'de [doğrudan uygulama atamasını](/graph/api/resources/approleassignment) öğrenmek için Microsoft Graph API 'sini kullanarak bu kullanıcılara verilen erişimi belirleyebilirsiniz.


### <a name="enumerate-application-specific-permissions"></a>Uygulamaya özgü izinleri listeleme

Uygulamaya özgü izin numaralandırması da gerçekleştirebilirsiniz. Örneğin, [bu betiği](https://gallery.technet.microsoft.com/office/SharePoint-Online-c9ec4f64)kullanarak SharePoint Online için program aracılığıyla bir izin raporu oluşturabilirsiniz.

Tüm dış erişimlerinizi tamamen bilmeniz için, tüm işletme duyarlı ve iş açısından kritik uygulamalarınız için erişimi özellikle araştırın.

### <a name="detect-ad-hoc-sharing"></a>Geçici paylaşımı Algıla
E-postanız ve ağ planlarınız bunu etkinleştiriyorsa, e-posta ile veya izinsiz yazılım hizmet olarak yazılım (SaaS) uygulamaları aracılığıyla paylaşılmakta olan içeriği inceleyebilirsiniz. [Microsoft 365 veri kaybı koruması](/microsoft-365/compliance/data-loss-prevention-policies) , hassas bilgilerin Microsoft 365 altyapınızda yanlışlıkla paylaşılmasını belirlemenize, engellemenize ve izlemenize yardımcı olur. [Microsoft Cloud App Security](https://www.microsoft.com/microsoft-365/enterprise-mobility-security/cloud-app-security) , ortamınızda yetkisiz SaaS uygulamalarının kullanımını belirlemenize yardımcı olabilir.

## <a name="next-steps"></a>Sonraki adımlar

Kaynaklara dışarıdan erişimi güvenli hale getirmek için aşağıdaki makalelere bakın. Eylemleri listelenen sırayla yapmanızı öneririz.

1. [Dış erişim için güvenlik durunuzu belirleme](1-secure-access-posture.md)

2. [Geçerli durumlarınızı bulun](2-secure-access-current-state.md) (burada bulabilirsiniz.)

3. [İdare planı oluşturma](3-secure-access-plan.md)

4. [Güvenlik için grupları kullanma](4-secure-access-groups.md)

5. [Azure AD B2B 'ye geçiş](5-secure-access-b2b.md)

6. [Yetkilendirme yönetimiyle güvenli erişim](6-secure-access-entitlement-managment.md)

7. [Koşullu erişim ilkeleriyle güvenli erişim](7-secure-access-conditional-access.md)

8. [Duyarlılık etiketleriyle güvenli erişim](8-secure-access-sensitivity-labels.md)

9. [Microsoft ekipleri, OneDrive ve SharePoint 'e güvenli erişim](9-secure-access-teams-sharepoint.md)