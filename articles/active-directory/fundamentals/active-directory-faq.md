---
title: Sık sorulan sorular (SSS)-Azure Active Directory | Microsoft Docs
description: Azure ve Azure Active Directory, parola yönetimi ve uygulama erişimi hakkında sık sorulan sorular ve yanıtlar.
services: active-directory
author: ajburnle
manager: daveba
ms.assetid: b8207760-9714-4871-93d5-f9893de31c8f
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: troubleshooting
ms.date: 11/12/2018
ms.author: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: a3b9f76eba9697d6ad47ecf8dc51689538923f88
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101094484"
---
# <a name="frequently-asked-questions-about-azure-active-directory"></a>Azure Active Directory hakkında sık sorulan sorular
Azure Active Directory (Azure AD), kimlik, erişim yönetimi ve güvenliği tüm yönleriyle kapsayan bir hizmet olarak kimlik (IDaaS) çözümüdür.

Daha fazla bilgi için bkz. [Azure Active Directory nedir?](active-directory-whatis.md).


## <a name="access-azure-and-azure-active-directory"></a>Azure ve Azure Active Directory erişimi
**S: Azure portal Azure AD 'ye erişmeye çalıştığımda neden "abonelik bulunamadı" ifadesini alıyorum?**

**Y:** Azure portalına erişmek için her kullanıcının bir Azure aboneliğiyle birlikte izinleri olmalıdır. Ücretli Microsoft 365 veya Azure AD aboneliğiniz yoksa ücretsiz bir [Azure hesabı](https://azure.microsoft.com/free/
) veya ücretli bir abonelik etkinleştirmeniz gerekir.

Daha fazla bilgi için bkz.

* [Azure aboneliklerinin Azure Active Directory ile ilişkisi](active-directory-how-subscriptions-associated-directory.md)

---
**S: Azure AD, Microsoft 365 ve Azure arasındaki ilişki nedir?**

**Y:** Azure AD, tüm web hizmetlerine yönelik ortak kimlik ve erişim işlevleri sunar. Microsoft 365, Microsoft Azure, Intune veya diğerleri kullanıyor olmanıza bakılmaksızın, bu hizmetler için oturum açma ve erişim yönetiminin sağlanmasına yardımcı olmak üzere Azure AD 'yi zaten kullanıyorsunuz.

Web hizmetlerini kullanacak şekilde ayarlanmış tüm kullanıcılar, bir veya daha fazla Azure AD örneğinde kullanıcı hesabı olarak tanımlanır. Bulut uygulama erişimi gibi ücretsiz Azure AD özellikleri için bu hesapları ayarlayabilirsiniz.

Enterprise Mobility + Security gibi Azure AD ücretli hizmetler, Microsoft 365 ve Microsoft Azure gibi diğer Web hizmetlerini kapsamlı kurumsal ölçekte yönetim ve güvenlik çözümleriyle tamamlayabilir.

---

**S: sahip ve genel yönetici arasındaki farklar nelerdir?**

Y **:** Varsayılan olarak, bir Azure aboneliğine kaydolan kişiye Azure kaynakları için sahip rolü atanır. Bir sahip, Azure aboneliğinin ilişkili olduğu dizinden Microsoft hesabı veya bir iş ya da okul hesabı kullanabilir.  Bu rol Azure portaldaki hizmetleri yönetme yetkisine sahiptir.

Başkalarının aynı aboneliği kullanarak oturum açması ve hizmetlere erişmesi gerekiyorsa, bu kullanıcılara uygun [yerleşik rolü](../../role-based-access-control/built-in-roles.md)de atayabilirsiniz. Daha fazla bilgi için bkz. [Azure Portal kullanarak Azure rolleri atama](../../role-based-access-control/role-assignments-portal.md).

Varsayılan olarak, bir Azure aboneliğine kaydolan kişiye, dizin için genel yönetici rolü atanır. Genel yönetici, tüm Azure AD dizin özelliklerine erişebilir. Azure AD 'nin, dizin ve kimlikle ilgili özellikleri yönetmek için farklı bir yönetici rolü kümesi vardır. Bu yöneticiler Azure portal çeşitli özelliklere erişebilir. Yönetici rolü, Kullanıcı oluşturma veya düzenleme, başkalarına yönetici rolleri atama, Kullanıcı parolalarını sıfırlama, kullanıcı lisanslarını yönetme veya etki alanlarını yönetme gibi neler yapabileceğini belirler.  Azure AD dizin yöneticileri ve rolleri hakkında daha fazla bilgi için, bkz. [Azure Active Directory yönetici rollerine Kullanıcı atama](active-directory-users-assign-role-azure-portal.md) ve [Azure Active Directory yönetici rolleri atama](../roles/permissions-reference.md).

Ayrıca, Azure AD ücretli hizmetler, Microsoft 365 ve Microsoft Azure gibi diğer Web hizmetlerini kapsamlı kurumsal ölçekte yönetim ve güvenlik çözümleriyle Enterprise Mobility + Security.

---
**S: Azure AD kullanıcı lisanslarımın ne zaman sona ereceğini gösteren bir rapor var mı?**

Y **:** Eşleşen.  Bu özellik şu an kullanılamıyor.

---

## <a name="get-started-with-hybrid-azure-ad"></a>Karma Azure AD ile çalışmaya başlama


**S: Ortak çalışan olarak eklendiğimde kiracıdan nasıl ayrılabilirim?**

**Y:** Başka bir kuruluşun kiracısına ortak çalışan olarak eklendiğinizde, kiracılar arasında geçiş yapmak için sağ üst köşedeki "kiracı değiştiricisi" seçeneğini kullanabilirsiniz.  Şu anda, davet eden kuruluştan ayrılma seçeneği mevcut değildir ve Microsoft bu işlevselliği sunmak için çalışmaktadır.  Bu özellik kullanılabilir oluncaya kadar, davet eden kuruluştan sizi kiracısından kaldırmasını isteyebilirsiniz.

---
**S: Şirket içi dizinimi Azure AD'ye nasıl bağlayabilirim?**

**Y:** Şirket içi dizininizi Azure AD'ye bağlamak için Azure AD Connect'i kullanabilirsiniz.

Daha fazla bilgi için bkz. [Şirket içi kimliklerinizi Azure Active Directory ile tümleştirme](../hybrid/whatis-hybrid-identity.md).

---
**S: Şirket içi dizinim ve bulut uygulamalarım arasında SSO'yu nasıl ayarlarım?**

**Y:** Çoklu oturum açmayı (SSO) yalnızca şirket içi dizininiz ve Azure AD arasında ayarlamanız yeterlidir. Bulut uygulamalarınıza Azure AD üzerinden eriştiğiniz sürece, hizmet otomatik olarak kullanıcılarınızı şirket içi kimlik bilgileriyle doğru şekilde kimlik doğrulaması gerçekleştirmeye yönlendirir.

Şirket içinden SSO uygulamak, Active Directory Federasyon Hizmetleri (AD FS) (AD FS) gibi Federasyon çözümleriyle veya parola karması eşitlemesini yapılandırarak kolayca elde edilebilir. Azure AD Connect yapılandırma sihirbazını kullanarak her iki seçeneği de kolayca dağıtabilirsiniz.

Daha fazla bilgi için bkz. [Şirket içi kimliklerinizi Azure Active Directory ile tümleştirme](../hybrid/whatis-hybrid-identity.md).

---
**S: Azure AD, kuruluşumdaki kullanıcılar için bir self servis portal sağlar mı?**

**Y:** Evet, AD size kullanıcı self servis ve uygulama erişimi için [Azure AD Erişim Paneli](https://myapps.microsoft.com)'ni sağlar. Microsoft 365 müşterisiyseniz, [Office 365 portalında](https://portal.office.com)aynı yeteneklerin birçoğunu bulabilirsiniz.

Daha fazla bilgi için bkz. [Erişim Paneli'ne Giriş](../user-help/my-apps-portal-end-user-access.md).

---
**S: Azure AD, şirket içi altyapımı yönetmeme yardımcı olur mu?**

**Y:** Evet. Azure AD Premium sürümü size Azure AD Connect Health olanağını sağlar. Azure AD Connect Health, şirket içi kimlik altyapınızı ve eşitleme hizmetlerini izlemenize ve daha iyi kavramanıza yardımcı olur.  

Daha fazla bilgi için bkz. [Bulutta şirket içi kimlik altyapınızı ve eşitleme hizmetlerinizi izleme](../hybrid/whatis-azure-ad-connect.md).  

---
## <a name="password-management"></a>Parola yönetimi
**S: Azure AD parola geri yazma özelliğini parola eşitleme olmadan kullanabilir miyim? (Bu senaryoda, Azure AD self servis parola sıfırlama (SSPR) ile parola geri yazma ve bu parolaları bulutta depolamama kullanımı mümkün midir?)**

**Y:** Geri yazmayı etkinleştirmek için Active Directory parolalarınızı Azure AD ile eşitlemeniz gerekmez. Birleştirilmiş bir ortamda Azure AD çoklu oturum açma (SSO), kullanıcının kimliğini doğrulamak için şirket içi dizini kullanır. Bu senaryoda, şirket içi parolanın Azure AD'de izlenmesi gerekmez.

---
**S: Bir parolanın Active Directory şirket içi konumuna geri yazılması ne kadar zaman alır?**

**Y:** Parola geri yazma işlemi gerçek zamanlı olarak gerçekleşir.

Daha fazla bilgi için bkz. [Parola yönetimine başlarken](../authentication/tutorial-enable-sspr.md).

---
**S: Parola geri yazma özelliğini bir yönetici tarafından yönetilen parolalarla kullanabilir miyim?**

**Y:** Evet, parola geri yazma özelliğini etkinleştirdiyseniz bir yönetici tarafından gerçekleştirilen parola işlemleri, şirket içi ortamınıza geri yazılır.  

<a name="for-more-answers-to-password-related-questions-see-password-management-frequently-asked-questions"></a>Parola ile ilgili daha fazla soru yanıtı için bkz. [Parola yönetimi ile ilgili sık sorulan sorular](../authentication/active-directory-passwords-faq.md).
---
**S: parolamı değiştirmeye çalışırken mevcut Microsoft 365/Azure AD parolamı hatırlayamıyorum ne yapabilirim?**

**Y:** Bu tür bir durum için birkaç seçenek vardır.  Varsa, self servis parola sıfırlama (SSPR) özelliğini kullanın.  SSPR’nin çalışıp çalışmaması nasıl yapılandırıldığına bağlıdır.  Daha fazla bilgi için bkz. [Parola sıfırlama portalının çalışması](../authentication/howto-sspr-deployment.md).

Microsoft 365 kullanıcılar için yöneticiniz, [Kullanıcı parolalarını sıfırlama](https://support.office.com/article/Admins-Reset-user-passwords-7A5D073B-7FAE-4AA5-8F96-9ECD041ABA9C?ui=en-US&rs=en-US&ad=US)bölümünde özetlenen adımları kullanarak parolayı sıfırlayabilir.

Azure AD hesapları için yöneticiler aşağıdakilerden birini kullanarak parolaları sıfırlayabilir:

- [Azure portalda hesapları sıfırlama](active-directory-users-reset-password-azure-portal.md)
- [PowerShell'i kullanma](/powershell/module/msonline/set-msoluserpassword)


---
## <a name="security"></a>Güvenlik
**S: Hesaplar belirli sayıda girişim başarısız olduktan sonra kilitleniyor mu, yoksa kullanılan daha karmaşık bir strateji mi var?**

Hesapları kilitlemek için daha karmaşık bir strateji kullanırız.  Bu strateji, isteğin IP adresini ve girilen parolaları temel alır. Kilitleme süresi ayrıca girişimin bir saldırı olma olasılığına göre artar.  

**S: belirli (ortak) parolalar ' Bu parola birçok kez kullanıldı ' iletileriyle reddedildi, bu, geçerli Active Directory 'de kullanılan parolalara başvuruyor mu?**

Bu, "Password" ve "123456" öğesinin herhangi bir çeşitleri gibi genel olarak ortak olan parolalara başvurur.

**S: Güvenilmez kaynaklardan (botnet, tor uç noktası) gelen oturum açma istekleri bir B2C kiracısında engellenir mi veya bir Temel ya da Premium sürüm kiracı gerekir mi?**

İstekleri filtreleyen ve botnetlere karşı koruma sağlayıp tüm B2C kiracılarına uygulanan bir ağ geçidine sahibiz.

## <a name="application-access"></a>Uygulama erişimi

**S: Azure AD ile önceden tümleştirilmiş olan uygulamaların ve özelliklerinin listesini nereden bulabilirim?**

**Y:** Azure AD, Microsoft'a, uygulama hizmeti sağlayıcılarına ve iş ortaklarına ait 2.600'ü aşkın önceden tümleştirilmiş uygulama içerir. Önceden tümleştirilmiş tüm uygulamalar çoklu oturum açmayı (SSO) destekler. SSO, uygulamalarınıza erişmek için kurumsal kimlik bilgilerinizi kullanmanıza olanak tanır. Uygulamalardan bazıları aynı zamanda otomatik hazırlama ve sağlamayı kaldırma özelliklerini destekler.

Önceden tümleştirilmiş uygulamaların tam listesi için bkz. [Active Directory Marketi](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureActiveDirectory).

---
**S: İhtiyacım olan uygulama Azure AD marketinde yoksa ne olur?**

**Y:** Azure AD Premium ile istediğiniz uygulamayı ekleyip yapılandırabilirsiniz. Uygulamanızın özelliklerine ve tercihlerinize bağlı olarak SSO ve otomatik sağlamayı yapılandırabilirsiniz.  

Daha fazla bilgi için bkz.

* [Azure Active Directory uygulama galerisinde bulunmayan uygulamalar için çoklu oturum açmayı yapılandırma](../manage-apps/configure-saml-single-sign-on.md)
* [Kullanıcıların ve grupların Azure Active Directory'den uygulamalara otomatik olarak hazırlanmasını etkinleştirmek için SCIM'yi kullanma](../app-provisioning/use-scim-to-provision-users-and-groups.md)

---
**S: Kullanıcılar Azure AD'yi kullanarak uygulamalarda nasıl oturum açar?**

**Y:** Azure AD, kullanıcılara uygulamalarını görüntülemeleri ve bunlara erişmeleri için birçok yol sağlar. Bunlardan bazıları aşağıda verilmiştir:

* Azure AD erişim paneli
* Microsoft 365 uygulama başlatıcısı
* Birleştirilmiş uygulamalarda doğrudan oturum açma
* Birleştirilmiş, parola tabanlı veya var olan uygulamalara yönelik ayrıntılı bağlantılar

Daha fazla bilgi için bkz. [uygulamalar Için son kullanıcı deneyimleri](../manage-apps/end-user-experiences.md).

---
**S: Azure AD, uygulamalar için kimlik doğrulaması ve çoklu oturum açmaya ne gibi farklı yollarla olanak tanır?**

**Y:** Azure AD; SAML 2.0, OpenID Connect, OAuth 2.0 ve WS-Federasyon gibi birçok standartlaştırılmış kimlik doğrulaması ve yetkilendirme protokolünü destekler. Azure AD aynı zamanda, yalnızca form tabanlı kimlik doğrulamasını destekleyen uygulamalar için parola kasası oluşturma ve otomatik oturum açma işlevlerini de destekler.  

Daha fazla bilgi için bkz.

* [Azure AD için Kimlik Doğrulama Senaryoları](../develop/authentication-vs-authorization.md)
* [Active Directory kimlik doğrulama protokolleri](/previous-versions/azure/dn151124(v=azure.100))
* [Azure AD 'de uygulamalar için çoklu oturum açma](../manage-apps/what-is-single-sign-on.md)

---
**S: şirket içinde çalıştırdığım uygulamaları ekleyebilir miyim?**

**Y:** Azure AD Uygulama Ara Sunucusu, size seçtiğiniz şirket içi web uygulamaları için kolay ve güvenli erişim sağlar. Bu uygulamalara, Azure AD'de hizmet olarak yazılım (SaaS) uygulamalarınıza eriştiğiniz gibi erişebilirsiniz. VPN kullanmanız veya ağ altyapınızı değiştirmeniz gerekmez.  

Daha fazla bilgi için bkz. [Şirket içi uygulamalara güvenli uzaktan erişim sağlama](../manage-apps/application-proxy.md).

---
**S: Belirli bir uygulamaya erişen kullanıcılar için çok faktörlü kimlik doğrulamasını nasıl isteyebilirim?**

Y **:** Azure AD koşullu erişimi sayesinde her bir uygulama için benzersiz bir erişim ilkesi atayabilirsiniz. İlkenizde, çok faktörlü kimlik doğrulamasını her zaman veya kullanıcılar yerel ağa bağlı olmadığında gerekli kılabilirsiniz.  

Daha fazla bilgi için bkz. [Microsoft 365 erişimi güvenli hale getirme ve Azure Active Directory bağlı diğer uygulamalar](../conditional-access/overview.md).

---
**S: SaaS uygulamaları için otomatik kullanıcı hazırlama nedir?**

**Y:** Birçok popüler bulut SaaS uygulamasında kullanıcı kimliklerinin oluşturulmasını, bakımını ve kaldırılmasını otomatik hale getirmek için Azure AD kullanın.

Daha fazla bilgi için bkz. [Azure Active Directory ile SaaS uygulamalarına kullanıcı hazırlama ve sağlamayı kaldırma işlemlerini otomatik hale getirme](../app-provisioning/user-provisioning.md).

---
**S:  Azure AD ile güvenli bir LDAP bağlantısı oluşturabilir miyim?**

Y **:**  Eşleşen. Azure AD, Hafif Dizin Erişim Protokolü (LDAP) protokolünü veya doğrudan Güvenli LDAP desteklemez. Ancak, Azure AD kiracınızda LDAP bağlantısı elde etmek için Azure ağ üzerinden düzgün şekilde yapılandırılmış ağ güvenlik grupları ile Azure AD Domain Services (Azure AD DS) örneğinin etkinleştirilmesi mümkündür. Daha fazla bilgi için bkz. [Azure Active Directory Domain Services yönetilen bir etki alanı için GÜVENLI LDAP yapılandırma](../../active-directory-domain-services/tutorial-configure-ldaps.md)
