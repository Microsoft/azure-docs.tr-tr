---
title: Azure AD rolleri için en iyi uygulamalar-Azure Active Directory
description: Azure Active Directory rollerini kullanmak için en iyi uygulamalar.
services: active-directory
author: rolyon
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: conceptual
ms.date: 03/28/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 78f4642b8f9f1ede65766a0026940c0af0f01ec2
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106112019"
---
# <a name="best-practices-for-azure-ad-roles"></a>Azure AD rolleri için en iyi uygulamalar

Bu makalede Azure Active Directory rol tabanlı erişim denetimi (Azure AD RBAC) kullanmak için en iyi uygulamalardan bazıları açıklanmaktadır. Bu en iyi uygulamalar, Azure AD RBAC ile deneyimimizden ve bizim gibi müşterilerin deneyimlerinden türetilir. [Azure AD 'de karma ve bulut dağıtımları için ayrıcalıklı erişimin güvenliğini sağlamaya](security-planning.md)yönelik ayrıntılı güvenlik Kılavuzumuzu da okumanızı öneririz.

## <a name="1-manage-to-least-privilege"></a>1. en az ayrıcalığa Yönet

Erişim denetimi stratejinizi planlarken, en az ayrıcalığa göre yönetilmesi en iyi uygulamadır. En az ayrıcalık, yöneticilerinize işlerini yapmak için ihtiyaç duydukları izni verdiğiniz anlamına gelir. Yöneticilerinize bir rol atarken göz önünde bulundurulması gereken üç nokta vardır: belirli bir süre boyunca belirli bir kapsam üzerinde belirli bir izin kümesi. Daha geniş bir rolün daha kolay bir şekilde daha kolay görünse bile daha geniş kapsamlar atamaktan kaçının. Rolleri ve kapsamları sınırlayarak, güvenlik sorumlusu tehlikeye girerse, hangi kaynakların risk altında olduğunu sınırlayabilirsiniz. Azure AD RBAC, 65 üzeri [yerleşik rolü](permissions-reference.md)destekler. Kullanıcılar, gruplar ve uygulamalar gibi dizin nesnelerini yönetmek ve ayrıca Exchange, SharePoint ve Intune gibi Microsoft 365 hizmetleri yönetmek için Azure AD rolleri vardır. Azure AD yerleşik rollerini daha iyi anlamak için bkz. [Azure Active Directory rolleri anlama](concept-understand-roles.md). Gereksinimlerinize uygun bir yerleşik rol yoksa kendi [özel rollerinizi](custom-create.md)oluşturabilirsiniz.  
 
### <a name="finding-the-right-roles"></a>Doğru rolleri bulma

Doğru rolü bulmanıza yardımcı olması için aşağıdaki adımları izleyin.

1. Azure portal, Azure AD rollerinin listesini görmek için [Roller ve yöneticiler](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators) ' i açın.

1. Rol listesini daraltmak için **hizmet** filtresini kullanın.

    ![Azure AD 'de hizmet filtresi açık olan roller ve yöneticiler sayfası](./media/best-practices/roles-administrators.png)

1. [Azure AD yerleşik roller](permissions-reference.md) belgelerine bakın. Her rolle ilişkili izinler, daha iyi okunabilirlik için birlikte listelenmiştir. Rol izinlerinin yapısını ve anlamını anlamak için bkz. [rol izinlerini anlama](permissions-reference.md#how-to-understand-role-permissions).

1. [Görev belgelerine göre en az ayrıcalıklı rol](delegate-by-task.md) bölümüne bakın.

## <a name="2-use-privileged-identity-management-to-grant-just-in-time-access"></a>2. tam zamanında erişim vermek için Privileged Identity Management kullanın

En az ayrıcalığa ait prensiplerin biri yalnızca belirli bir süre için erişime verilmelidir. [Azure AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) , yöneticilerinize tam zamanında erişim vermenize olanak tanır. Microsoft, Azure AD 'de PıM 'yi etkinleştirmenizi önerir. PıM 'yi kullanarak bir Kullanıcı Azure AD rolünün uygun bir üyesini oluşturabilir. Daha sonra, rolünü her kullanması gerektiğinde sınırlı bir zaman çerçevesi için etkinleştirebilir. Zaman dilimi zaman aşımına uğradığında ayrıcalıklı erişim otomatik olarak kaldırılır. Ayrıca, Kullanıcı rolü atamasını etkinleştirdiğinde, [PIM ayarlarını](../privileged-identity-management/pim-how-to-change-default-settings.md) onay gerektirecek veya bildirim e-postaları alacak şekilde yapılandırabilirsiniz. Bildirimler, son derece ayrıcalıklı rollere yeni kullanıcılar eklendiğinde bir uyarı verir. 

## <a name="3-turn-on-multi-factor-authentication-for-all-your-administrator-accounts"></a>3. tüm yönetici hesaplarınızda Multi-Factor Authentication 'ı açın

[Çalışmalarımıza göre](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/your-pa-word-doesn-t-matter/ba-p/731984), Multi-Factor AUTHENTICATION (MFA) kullandığınızda hesabınız% 99,9 daha az olabilir. 
 
Azure AD rollerinde MFA 'yı etkinleştirmek için iki yöntem kullanabilirsiniz:
- Privileged Identity Management [rol ayarları](../privileged-identity-management/pim-how-to-change-default-settings.md)
- [Koşullu Erişim](../conditional-access/howto-conditional-access-policy-admin-mfa.md)

## <a name="4-configure-recurring-access-reviews-to-revoke-unneeded-permissions-over-time"></a>4. zaman içinde gereksiz izinleri iptal etmek için yinelenen erişim incelemelerini yapılandırın

Erişim gözden geçirmeleri, kuruluşların yalnızca doğru kişilerin erişmeye devam ettiğinden emin olmak için yöneticinin erişimi düzenli olarak incelemesini sağlar. Aşağıdaki nedenlerden dolayı yöneticileriniz düzenli olarak denetim altına alındır:
- Kötü amaçlı bir aktör bir hesabın güvenliğini tehlikeye atabilir.
- İnsanlar takımları bir şirket içinde taşır. Denetim yoksa, zaman içinde gereksiz erişimi bir şekilde kullanabilir.
 
Rollere yönelik erişim incelemeleri hakkında daha fazla bilgi için bkz. [PIM 'de Azure AD rolleri için erişim gözden geçirmesi oluşturma](../privileged-identity-management/pim-how-to-start-security-review.md). Rol atanmış grupların erişim gözden geçirmeleri hakkında daha fazla bilgi için bkz. [Azure AD erişim gözden geçirmeleri içindeki gruplar ve uygulamalar için erişim Incelemesi oluşturma](../governance/create-access-review.md).

## <a name="5-limit-the-number-of-global-administrators-to-less-than-5"></a>5. genel yönetici sayısını 5 ' ten az olacak şekilde sınırlayın

En iyi uygulama olarak, Microsoft, genel yönetici rolünü kuruluşunuzda **beşten az kişiden fazlasına** atamanızı önerir. Genel Yöneticiler anahtarları Krallık 'a tutar ve saldırı yüzeyini düşük tutmak için en iyi ilgilendiğiniz bir yöntemdir. Daha önce belirtildiği gibi, bu hesapların hepsi Multi-Factor Authentication ile korunmalıdır.

Varsayılan olarak, bir Kullanıcı bir Microsoft bulut hizmetine kaydolduğunda, bir Azure AD kiracısı oluşturulur ve Kullanıcı genel Yöneticiler rolünün bir üyesi yapılır. Genel yönetici rolüne atanan kullanıcılar, Azure AD kuruluşunuzda her yönetim ayarını okuyabilir ve değiştirebilir. Küresel Yöneticiler, birkaç özel durum dışında Microsoft 365 kuruluşunuzdaki tüm yapılandırma ayarlarını okuyabilir ve değiştirebilir. Genel Yöneticiler, verileri okumak için erişimini yükseltebilme özelliğine de sahiptir.

Microsoft, genel yönetici rolüne kalıcı olarak atanmış iki kesme camı hesabını tutmanızı önerir. Bu hesapların, [Azure AD 'de acil durum erişim hesaplarını yönetme](../roles/security-emergency-access.md)bölümünde açıklandığı gibi, oturum açmak için normal yönetim hesaplarınızla aynı Multi-Factor Authentication mekanizmasına gerek olmadığından emin olun. 

## <a name="6-use-groups-for-azure-ad-role-assignments-and-delegate-the-role-assignment"></a>6. Azure AD rol atamaları için grupları kullanma ve rol atamasını temsilci seçme

Gruplardan faydalanan bir dış idare sisteminiz varsa, bireysel kullanıcılar yerine Azure AD gruplarına roller atamayı göz önünde bulundurmanız gerekir. Ayrıca, bu ayrıcalıklı gruplarda bir veya daha fazla üye bulunmadığından emin olmak için, PıM 'de rol atanabilir grupları yönetebilirsiniz. Daha fazla bilgi için bkz. [ayrıcalıklı erişim Için yönetim özellikleri Azure AD grupları](../privileged-identity-management/groups-features.md).

Rol atanabilir gruplara bir sahip atayabilirsiniz. Bu sahip, gruba ne ekleneceğini veya gruptan kimin kaldırıldığını belirler, bu nedenle dolaylı olarak rol atamasını alan karar verir. Bu şekilde, genel yönetici veya ayrıcalıklı rol yöneticisi, gruplar kullanarak rol başına rol yönetimine temsilci olarak atayabilirler. Daha fazla bilgi için bkz. [Azure Active Directory rol atamalarını yönetmek için bulut gruplarını kullanma](groups-concept.md).

## <a name="7-activate-multiple-roles-at-once-using-privileged-access-groups"></a>7. ayrıcalıklı erişim grupları kullanarak birden çok rolü aynı anda etkinleştirin

Bir kişinin, PıM aracılığıyla Azure AD rollerine beş veya altı uygun ataması olması olabilir. Her bir rolü tek tek etkinleştirmek zorunda kalacak ve bu da üretkenliği azaltabilir. Hala kötüleşseniz de, bu, soruna neden olan onlarca veya yüzlerce Azure kaynağına atanmış olabilir.
 
Bu durumda, [ayrıcalıklı erişim grupları](../privileged-identity-management/groups-features.md)kullanmanız gerekir. Ayrıcalıklı bir erişim grubu oluşturun ve bu gruba birden fazla role (Azure AD ve/veya Azure) kalıcı erişim izni verin. Bu kullanıcıya bu grubun uygun bir üyesini veya sahibini yapın. Yalnızca tek bir etkinleştirme sayesinde, bu kullanıcılar tüm bağlantılı kaynaklara erişebilir.

![Aynı anda birden çok rolü etkinleştirmeyi gösteren ayrıcalıklı erişim grubu diyagramı](./media/best-practices/privileged-access-group.png)

## <a name="8-use-cloud-native-accounts-for-azure-ad-roles"></a>8. Azure AD rolleri için bulut Yerel hesaplarını kullanın

Azure AD rol atamaları için şirket içi eşitlenmiş hesaplar kullanmaktan kaçının. Şirket içi hesabınız tehlikeye girerse, Azure AD kaynaklarınızın de güvenliğini tehlikeye atabilir.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure AD'de karma ve bulut dağıtımları için ayrıcalıklı erişim güvenliğini sağlama](security-planning.md)