---
title: Sık sorulan sorular (SSS) - Azure Active Directory | Microsoft Docs
description: Ortak hakkında sorular ve cevaplar Azure ve Azure Active Directory, parola yönetimi ve uygulama erişimi.
services: active-directory
author: msaburnley
manager: daveba
ms.assetid: b8207760-9714-4871-93d5-f9893de31c8f
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 11/12/2018
ms.author: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4c1ee5e849d8004f828a2d92d728ad7925fc05c4
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68693944"
---
# <a name="frequently-asked-questions-about-azure-active-directory"></a>Azure Active Directory hakkında sık sorulan sorular
Azure Active Directory (Azure AD), kimlik, erişim yönetimi ve güvenliği tüm yönleriyle kapsayan bir hizmet olarak kimlik (IDaaS) çözümüdür.

Daha fazla bilgi için bkz. [Azure Active Directory nedir?](active-directory-whatis.md).


## <a name="access-azure-and-azure-active-directory"></a>Azure ve Azure Active Directory erişimi
**S: Azure portal Azure AD 'ye erişmeye çalıştığımda neden "abonelik bulunamadı" ifadesini alıyorum?**

**C:** Azure portal erişmek için her kullanıcının bir Azure aboneliğiyle izinleri olması gerekir. Ücretli bir Office 365 veya Azure AD aboneliğiniz varsa, tek seferlik etkinleştirme adımı için [https://aka.ms/accessAAD](https://aka.ms/accessAAD) sayfasına gidin. Aksi takdirde, ücretsiz bir [Azure hesabı](https://azure.microsoft.com/pricing/free-trial/) veya ücretli aboneliği etkinleştirmeniz gerekir.

Daha fazla bilgi için bkz.

* [Azure aboneliklerinin Azure Active Directory ile ilişkisi](active-directory-how-subscriptions-associated-directory.md)

---
**S: Azure AD, Office 365 ve Azure arasındaki ilişki nedir?**

**C:** Azure AD, tüm Web hizmetlerine yönelik ortak kimlik ve erişim özellikleri sağlar. Office 365, Microsoft Azure, Intune veya diğer uygulamalardan hangisini kullanırsanız kullanın bu hizmetlerin tümü için oturum açma ve erişim yönetimini etkinleştirmek üzere zaten Azure AD'yi kullanırsınız.

Web hizmetlerini kullanacak şekilde ayarlanmış tüm kullanıcılar, bir veya daha fazla Azure AD örneğinde kullanıcı hesabı olarak tanımlanır. Bulut uygulama erişimi gibi ücretsiz Azure AD özellikleri için bu hesapları ayarlayabilirsiniz.

Enterprise Mobility + Security gibi ücretli Azure AD hizmetleri, kurumsal ölçekte kapsamlı yönetim ve güvenlik çözümleriyle Office 365 ve Microsoft Azure gibi diğer web hizmetlerini tamamlar.

---

**S:  Sahip ve genel yönetici arasındaki farklar nelerdir?**

**C:** Varsayılan olarak, bir Azure aboneliğine kaydolan kişiye Azure kaynakları için sahip rolü atanır. Sahibi, bir Microsoft hesabı ya da Azure aboneliğinin ilişkili olduğu dizinden bir iş veya Okul hesabı kullanabilirsiniz.  Bu rol Azure portaldaki hizmetleri yönetme yetkisine sahiptir.

Başkalarının oturum açın ve aynı aboneliği kullanarak hizmetlere erişmesi gerekiyorsa, bunları uygun atayabilirsiniz [yerleşik rol](../../role-based-access-control/built-in-roles.md). Ek bilgi için bkz: [RBAC ve Azure portalını kullanarak erişimini yönetme](../../role-based-access-control/role-assignments-portal.md).

Varsayılan olarak, bir Azure aboneliği için kaydolan kişi dizin için genel Yönetici rolüne atanır. Genel yönetici, tüm Azure AD directory özelliklerine erişebilir. Azure AD dizin ve kimlikle ilgili özelliklerin yönetilmesine yönetici rolleri farklı bir dizi vardır. Bu Yöneticiler, Azure portalında çeşitli özelliklere erişim gerekir. Yönetici rolü, oluşturma veya kullanıcıları Düzenle, diğerlerine yönetici rolleri atama, kullanıcı parolalarını sıfırlama, kullanıcı lisanslarını yönetme veya etki alanlarını yönetme gibi yapabileceklerini belirler.  Azure AD dizin yöneticileri ve rolleri hakkında ek bilgi için bkz: [bir kullanıcı Azure Active Directory'de yönetici rolleri atama](active-directory-users-assign-role-azure-portal.md) ve [AzureActiveDirectory'deyöneticirolleriatama](../users-groups-roles/directory-assign-admin-roles.md).

Ayrıca, Enterprise Mobility + Security gibi ücretli Azure AD hizmetleri, kurumsal ölçekte kapsamlı yönetim ve güvenlik çözümleriyle Office 365 ve Microsoft Azure gibi diğer web hizmetlerini tamamlar.

---
**S: Azure AD Kullanıcı lisanslarım ne zaman sona ereceğini gösteren bir rapor var mı?**

**C:** Hayır.  Bu özellik şu an kullanılamıyor.

---

## <a name="get-started-with-hybrid-azure-ad"></a>Karma Azure AD ile çalışmaya başlama


**S: Ortak çalışan olarak eklendiğimde kiracıyı bırakmak Nasıl yaparım?.**

**C:** Başka bir kuruluşun kiracısına ortak çalışan olarak eklendiğinde, kiracılar arasında geçiş yapmak için sağ üst köşedeki "Kiracı değiştirici" i kullanabilirsiniz.  Şu anda, davet eden kuruluştan ayrılma seçeneği mevcut değildir ve Microsoft bu işlevselliği sunmak için çalışmaktadır.  Bu özellik kullanılabilir oluncaya kadar, davet eden kuruluştan sizi kiracısından kaldırmasını isteyebilirsiniz.

---
**S: Şirket içi dizinimi Azure AD 'ye nasıl bağlayabilirim?**

**C:** Azure AD Connect kullanarak, şirket içi dizininizi Azure AD 'ye bağlayabilirsiniz.

Daha fazla bilgi için bkz. [Şirket içi kimliklerinizi Azure Active Directory ile tümleştirme](../hybrid/whatis-hybrid-identity.md).

---
**S: Nasıl yaparım? şirket içi Dizinim ve bulut uygulamalarım arasında SSO 'yu ayarlamak mı istiyorsunuz?**

**C:** Yalnızca şirket içi dizininiz ve Azure AD arasında çoklu oturum açma (SSO) ayarlamanız gerekir. Bulut uygulamalarınıza Azure AD üzerinden eriştiğiniz sürece, hizmet otomatik olarak kullanıcılarınızı şirket içi kimlik bilgileriyle doğru şekilde kimlik doğrulaması gerçekleştirmeye yönlendirir.

Şirket içi konumdan SSO uygulama işlemi, Active Directory Federation Services (ADFS) gibi federasyon çözümleri veya parola karma eşitlemesini yapılandırma işlemi ile kolayca gerçekleştirilebilir. Azure AD Connect yapılandırma sihirbazını kullanarak her iki seçeneği de kolayca dağıtabilirsiniz.

Daha fazla bilgi için bkz. [Şirket içi kimliklerinizi Azure Active Directory ile tümleştirme](../hybrid/whatis-hybrid-identity.md).

---
**S: Azure AD, kuruluşumdaki kullanıcılar için bir self servis portalı sağlar mi?**

**C:** Evet, Azure AD size Kullanıcı self servis ve uygulama erişimi için [Azure AD erişim paneli](https://myapps.microsoft.com) sağlar. Office 365 müşterisiyseniz, [office 365 portalında](https://portal.office.com)aynı yeteneklerin birçoğunu bulabilirsiniz.

Daha fazla bilgi için bkz. [Erişim Paneli'ne Giriş](../user-help/active-directory-saas-access-panel-introduction.md).

---
**S: Azure AD, şirket içi altyapımı yönetmeme yardımcı olur mu?**

**C:** Evet. Azure AD Premium sürümü size Azure AD Connect Health olanağını sağlar. Azure AD Connect Health, şirket içi kimlik altyapınızı ve eşitleme hizmetlerini izlemenize ve daha iyi kavramanıza yardımcı olur.  

Daha fazla bilgi için bkz. [Bulutta şirket içi kimlik altyapınızı ve eşitleme hizmetlerinizi izleme](../hybrid/whatis-hybrid-identity-health.md).  

---
## <a name="password-management"></a>Parola yönetimi
**S: Azure AD parola geri yazma 'yi parola eşitleme olmadan kullanabilir miyim? (Bu senaryoda Azure AD self servis parola sıfırlama (SSPR) özelliğinin parola geri yazma ile birlikte kullanılması ve parolaların buluta depolanmaması mümkün mü?)**

**C:** Geri yazmayı etkinleştirmek için Active Directory parolalarınızı Azure AD ile eşitlemeniz gerekmez. Birleştirilmiş bir ortamda Azure AD çoklu oturum açma (SSO), kullanıcının kimliğini doğrulamak için şirket içi dizini kullanır. Bu senaryoda, şirket içi parolanın Azure AD'de izlenmesi gerekmez.

---
**S: Bir parolanın şirket içi Active Directory geri yazılması ne kadar sürer?**

**C:** Parola geri yazma gerçek zamanlı olarak çalışır.

Daha fazla bilgi için bkz. [Parola yönetimine başlarken](../authentication/quickstart-sspr.md).

---
**S: Yönetici tarafından yönetilen parolalarla parola geri yazma kullanabilir miyim?**

**C:** Evet, parola geri yazma özelliği etkinse, bir yönetici tarafından gerçekleştirilen parola işlemleri şirket içi ortamınıza geri yazılır.  

<a name="for-more-answers-to-password-related-questions-see-password-management-frequently-asked-questionsauthenticationactive-directory-passwords-faqmd"></a>Parola ile ilgili daha fazla soru yanıtı için bkz. [Parola yönetimi ile ilgili sık sorulan sorular](../authentication/active-directory-passwords-faq.md).
---
**S:  Parolamı değiştirmeye çalışırken mevcut Office 365/Azure AD parolamı hatırlayamıyorum ne yapabilirim?**

**C:** Bu tür bir durum için birkaç seçenek vardır.  Varsa, self servis parola sıfırlama (SSPR) özelliğini kullanın.  SSPR’nin çalışıp çalışmaması nasıl yapılandırıldığına bağlıdır.  Daha fazla bilgi için bkz. [Parola sıfırlama portalının çalışması](../authentication/howto-sspr-deployment.md).

Office 365 kullanıcıları için yöneticiniz [Kullanıcı parolalarını sıfırlama](https://support.office.com/article/Admins-Reset-user-passwords-7A5D073B-7FAE-4AA5-8F96-9ECD041ABA9C?ui=en-US&rs=en-US&ad=US) bölümünde özetlenen adımları kullanarak parolayı sıfırlayabilir.

Azure AD hesapları için yöneticiler aşağıdakilerden birini kullanarak parolaları sıfırlayabilir:

- [Azure portalda hesapları sıfırlama](active-directory-users-reset-password-azure-portal.md)
- [PowerShell’i kullanma](/powershell/module/msonline/set-msoluserpassword?view=azureadps-1.0)


---
## <a name="security"></a>Güvenlik
**S: Hesaplar belirli bir sayıdaki başarısız girişimden sonra kilitlenir mi yoksa daha karmaşık bir strateji mı kullanılıyor?**

Hesapları kilitlemek için daha karmaşık bir strateji kullanırız.  Bu strateji, isteğin IP adresini ve girilen parolaları temel alır. Kilitleme süresi ayrıca girişimin bir saldırı olma olasılığına göre artar.  

**S:  Belirli (ortak) parolalar ' Bu parola birçok kez kullanıldı ' iletileriyle reddedildi, bu, geçerli Active Directory 'de kullanılan parolalara başvuruyor mu?**

Bu durum, parolaların “Parola” ve “123456” değerlerinin çeşitlemeleri gibi genel olarak yaygın olduğunu ifade eder.

**S: Bir B2C kiracısında dubemli kaynaklardan (botağları, Tor uç noktası) bir oturum açma isteği engellensin mi, yoksa bunun temel veya Premium sürüm kiracısı mi gerekiyor?**

İstekleri filtreleyen ve botnetlere karşı koruma sağlayıp tüm B2C kiracılarına uygulanan bir ağ geçidine sahibiz.

## <a name="application-access"></a>Uygulama erişimi

**S: Azure AD ile önceden tümleştirilmiş uygulamaların listesini ve bunların yeteneklerini nerede bulabilirim?**

**C:** Azure AD, Microsoft, uygulama hizmeti sağlayıcılarından ve iş ortaklarından 2.600 'ten daha önceden tümleştirilmiş uygulama içerir. Önceden tümleştirilmiş tüm uygulamalar çoklu oturum açmayı (SSO) destekler. SSO, uygulamalarınıza erişmek için kurumsal kimlik bilgilerinizi kullanmanıza olanak tanır. Uygulamalardan bazıları aynı zamanda otomatik hazırlama ve sağlamayı kaldırma özelliklerini destekler.

Önceden tümleştirilmiş uygulamaların tam listesi için bkz. [Active Directory Marketi](https://azure.microsoft.com/marketplace/active-directory/).

---
**S: İhtiyacım olan uygulama Azure AD marketi 'nde değilse ne olur?**

**C:** Azure AD Premium, istediğiniz herhangi bir uygulamayı ekleyip yapılandırabilirsiniz. Uygulamanızın özelliklerine ve tercihlerinize bağlı olarak, SSO'yu ve otomatik hazırlamayı yapılandırabilirsiniz.  

Daha fazla bilgi için bkz.

* [Azure Active Directory uygulama galerisinde bulunmayan uygulamalar için çoklu oturum açmayı yapılandırma](../manage-apps/configure-federated-single-sign-on-non-gallery-applications.md)
* [Kullanıcıların ve grupların Azure Active Directory'den uygulamalara otomatik olarak hazırlanmasını etkinleştirmek için SCIM'yi kullanma](../manage-apps/use-scim-to-provision-users-and-groups.md)

---
**S: Kullanıcılar Azure AD 'yi kullanarak uygulamalarda nasıl oturum açabilirler?**

**C:** Azure AD, kullanıcıların uygulamalarını görüntülemesi ve bunlara erişmek için çeşitli yollar sağlar; örneğin:

* Azure AD erişim paneli
* Office 365 uygulama başlatıcı
* Birleştirilmiş uygulamalarda doğrudan oturum açma
* Birleştirilmiş, parola tabanlı veya var olan uygulamalara yönelik ayrıntılı bağlantılar

Daha fazla bilgi için [uygulamalar için son kullanıcı deneyimlerini](../manage-apps/end-user-experiences.md).

---
**S: Azure AD 'nin uygulamalarda kimlik doğrulamasını ve çoklu oturum açmayı etkinleştirme yolları nelerdir?**

**C:** Azure AD; SAML 2,0, OpenID Connect, OAuth 2,0 ve WS-Federation gibi kimlik doğrulama ve yetkilendirme için birçok standartlaştırılmış protokolü destekler. Azure AD aynı zamanda, yalnızca form tabanlı kimlik doğrulamasını destekleyen uygulamalar için parola kasası oluşturma ve otomatik oturum açma işlevlerini de destekler.  

Daha fazla bilgi için bkz.

* [Azure AD için Kimlik Doğrulama Senaryoları](../develop/authentication-scenarios.md)
* [Active Directory kimlik doğrulama protokolleri](https://msdn.microsoft.com/library/azure/dn151124.aspx)
* [Azure AD uygulamaları için çoklu oturum açma](../manage-apps/what-is-single-sign-on.md)

---
**S: Şirket içinde çalıştırdığım uygulamaları ekleyebilir miyim?**

**C:** Azure AD Uygulama Ara Sunucusu, seçtiğiniz şirket içi Web uygulamalarına kolay ve güvenli bir şekilde erişmenizi sağlar. Bu uygulamalara, Azure AD'de hizmet olarak yazılım (SaaS) uygulamalarınıza eriştiğiniz gibi erişebilirsiniz. VPN kullanmanız veya ağ altyapınızı değiştirmeniz gerekmez.  

Daha fazla bilgi için bkz. [Şirket içi uygulamalara güvenli uzaktan erişim sağlama](../manage-apps/application-proxy.md).

---
**S: Nasıl yaparım?, belirli bir uygulamaya erişen kullanıcılar için çok faktörlü kimlik doğrulaması gerektirsin mi?**

**C:** Azure AD koşullu erişimi sayesinde her bir uygulama için benzersiz bir erişim ilkesi atayabilirsiniz. İlkenizde, çok faktörlü kimlik doğrulamasını her zaman veya kullanıcılar yerel ağa bağlı olmadığında gerekli kılabilirsiniz.  

Daha fazla bilgi için bkz. [Office 365'e ve Azure Active Directory'ye bağlı diğer uygulamalara erişimi güvence altına alma](../active-directory-conditional-access-azure-portal.md).

---
**S: SaaS uygulamaları için otomatik Kullanıcı hazırlama nedir?**

**C:** Birçok popüler bulut SaaS uygulamasında kullanıcı kimliklerinin oluşturulmasını, bakımını ve kaldırılmasını otomatik hale getirmek için Azure AD 'yi kullanın.

Daha fazla bilgi için bkz. [Azure Active Directory ile SaaS uygulamalarına kullanıcı hazırlama ve sağlamayı kaldırma işlemlerini otomatik hale getirme](../manage-apps/user-provisioning.md).

---
**S:  Azure AD ile güvenli bir LDAP bağlantısı ayarlayabilir miyim?**

**C:**  Hayır. Azure AD, Hafif Dizin Erişim Protokolü (LDAP) protokolünü veya doğrudan Güvenli LDAP desteklemez. Ancak, Azure AD kiracınızda LDAP bağlantısı elde etmek için Azure ağ üzerinden düzgün şekilde yapılandırılmış ağ güvenlik grupları ile Azure AD Domain Services (Azure AD DS) örneğinin etkinleştirilmesi mümkündür. Daha fazla bilgi için bkz. https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.
