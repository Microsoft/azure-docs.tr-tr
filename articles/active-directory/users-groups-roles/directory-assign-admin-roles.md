---
title: Yönetici rolü açıklamaları ve izinleri-Azure AD | Microsoft Docs
description: Yönetici rolü kullanıcı ekleyebilir, yönetici roller atayabilir, Kullanıcı parolalarını sıfırlayabilir, kullanıcı lisanslarını yönetebilir veya etki alanlarını yönetebilir.
services: active-directory
author: curtand
manager: daveba
search.appverid: MET150
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: reference
ms.date: 11/12/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5a53f2a0e5927a75c4d22ada5837da26bd8deeda
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74028281"
---
# <a name="administrator-role-permissions-in-azure-active-directory"></a>Azure Active Directory 'de yönetici rolü izinleri

Azure Active Directory (Azure AD) kullanarak, daha az ayrıcalıklı rollerdeki kimlik görevlerini yönetmek için sınırlı yöneticiler atayabilirsiniz. Yöneticiler, Kullanıcı ekleme veya değiştirme, yönetici rolleri atama, Kullanıcı parolalarını sıfırlama, kullanıcı lisanslarını yönetme ve etki alanı adlarını yönetme gibi amaçlar için atanabilir. Varsayılan Kullanıcı izinleri yalnızca Azure AD 'deki Kullanıcı ayarları ' nda değiştirilebilir.

## <a name="limit-the-use-of-global-administrator"></a>Genel yönetici kullanımını sınırlayın

Genel yönetici rolüne atanan kullanıcılar, Azure AD kuruluşunuzda her yönetim ayarını okuyabilir ve değiştirebilir. Varsayılan olarak, bir Azure aboneliğine kaydolan kişiye Azure AD kuruluşu için genel yönetici rolü atanır. Yalnızca genel Yöneticiler ve ayrıcalıklı rol yöneticileri yönetici rollerini temsil edebilir. İşletmenize yönelik riski azaltmak için, bu rolü kuruluşunuzdaki en az olası kişilere atamanızı öneririz.

En iyi uygulama olarak, bu rolü kuruluşunuzda 5 ' ten az kişiye atamanız önerilir. Kuruluşunuzda genel yönetici rolüne atanmış beş kullanıcı varsa, bunların kullanımını azaltmanın bazı yolları aşağıda verilmiştir.

### <a name="find-the-role-you-need"></a>İhtiyaç duyduğunuz rolü bulun

Birçok rolün bir listesini almanız gereken rolü bulmak için, Azure AD rol kategorilerine göre rollerin alt kümelerini gösterebilir. [Azure AD rolleri ve yöneticileri](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators) Için yeni **tür** filtreimize göz atın ve yalnızca seçili türdeki rolleri gösterin.

### <a name="a-role-exists-now-that-didnt-exist-when-you-assigned-the-global-administrator-role"></a>Genel yönetici rolünü atadığınızda artık mevcut olmayan bir rol var

Azure AD 'ye, bazı kullanıcıları küresel yöneticiye yükseltmiş bir seçenek olmayan daha ayrıntılı izinler sağlayan bir rol veya rol eklenmiş olabilir. Zaman içinde, yalnızca genel yönetici rolünün gerçekleştirebileceği görevleri gerçekleştiren ek roller sunuyoruz. Aşağıdaki [kullanılabilir rollere](#available-roles)yansıtıldığını görebilirsiniz.

## <a name="assign-or-remove-administrator-roles"></a>Yönetici rolleri atama veya kaldırma

Azure Active Directory ' de bir kullanıcıya yönetim rolleri atamayı öğrenmek için, bkz. [Azure Active Directory yönetici rollerini görüntüleme ve atama](directory-manage-roles-portal.md).

## <a name="available-roles"></a>Kullanılabilir roller

Aşağıdaki Yönetici rolleri kullanılabilir:

### <a name="application-administratorapplication-administrator-permissions"></a>[Uygulama Yöneticisi](#application-administrator-permissions)

Bu roldeki kullanıcılar kurumsal uygulamaların, uygulama kayıtlarının ve uygulama proxy ayarlarının tüm yönlerini oluşturabilir ve yönetebilir. Bu role atanan kullanıcıların yeni uygulama kayıtları veya kurumsal uygulamalar oluştururken sahip olarak eklenmediğini unutmayın.

> [!IMPORTANT]
> Bu rol, uygulama kimlik bilgilerini yönetme olanağı verir. Bu role atanan kullanıcılar, bir uygulamaya kimlik bilgileri ekleyebilir ve uygulamanın kimliğini taklit etmek için bu kimlik bilgilerini kullanabilir. Uygulamanın kimliğine Kullanıcı veya başka nesneler oluşturma veya güncelleştirme yeteneği gibi Azure Active Directory erişimi verildiyse, bu role atanan bir Kullanıcı, uygulamayı taklit ederken bu eylemleri gerçekleştirebilir. Uygulamanın kimliğini taklit etme özelliği, kullanıcının Azure AD 'de rol atamaları aracılığıyla yapabilecekleri ayrıcalık yükselmesine neden olabilir. Bir kullanıcıyı uygulama yöneticisi rolüne atamanın, uygulamanın kimliğini taklit etme olanağı sağladığını anlamak önemlidir.

Bu rol Ayrıca, Microsoft Graph ve Azure AD grafiğinde izin verilen izinler ve uygulama _izinleri için izin verme olanağı verir_ .

> [!IMPORTANT]
> Bu özel durum, _diğer_ uygulamalar için izinleri (örneğin, kaydettiğiniz üçüncü taraf uygulamalar veya uygulamalar) kabul edebilirsiniz, ancak Azure AD üzerinde izinler için izin vermenizi sağlar. Bu izinleri, uygulama kaydının bir parçası olarak _isteyebilirsiniz_ , ancak (örneğin, yarışmaya _izin vermek üzere_ ), bu IZINLER için bir Azure AD yöneticisi gerekir. Bu, kötü niyetli bir kullanıcının izinlerini kolayca yükseltebileceği anlamına gelir. Örneğin, tüm dizine yazabilme ve uygulamanın izinlerinin genel yönetici olmaya dönüşmesine yol açabilir.

### <a name="application-developerapplication-developer-permissions"></a>[Uygulama Geliştirici](#application-developer-permissions)

Bu roldeki kullanıcılar, "kullanıcılar uygulamaları kaydedebilirler" ayarı Hayır olarak ayarlanırsa uygulama kayıtları oluşturabilir. Bu rol Ayrıca, "kullanıcılar kendi adına şirket verilerine erişim izni verebilir" ayarında Hayır olarak ayarlanırsa, tek başına kendi adına onay izni verir. Bu role atanan kullanıcılar, yeni uygulama kayıtları veya kurumsal uygulamalar oluştururken sahip olarak eklenir.

### <a name="authentication-administratorauthentication-administrator-permissions"></a>[Kimlik doğrulama Yöneticisi](#authentication-administrator-permissions)

Bu role sahip kullanıcılar, parola olmayan kimlik bilgilerini ayarlayabilir veya sıfırlayabilir ve tüm kullanıcılar için parolaları güncelleştirebilir. Kimlik doğrulama yöneticileri, kullanıcıların mevcut parola olmayan kimlik bilgileri (örneğin, MFA veya FIDO) için yeniden **kaydolması ve CIHAZDAKI MFA 'yı unutmayı**zorunlu kılabilir ve bu, yönetici olmayan kullanıcıların bir sonraki oturum açma aşamasında MFA için istemde bulunur. yalnızca şu roller atandı:

* Kimlik doğrulama Yöneticisi
* Dizin okuyucuları
* Konuk davetci
* İleti Merkezi okuyucusu
* Rapor okuyucu

> [!IMPORTANT]
> Bu role sahip kullanıcılar, Azure Active Directory ' nin içindeki ve dışındaki gizli veya özel bilgilere veya kritik yapılandırmaya erişimi olabilecek kişiler için kimlik bilgilerini değiştirebilir. Bir kullanıcının kimlik bilgilerini değiştirmek, kullanıcının kimliğini ve izinlerini kabul etme imkanını ifade edebilir. Örneğin:
>
>- Sahip oldukları uygulamaların kimlik bilgilerini yönetebilen uygulama kaydı ve kurumsal uygulama sahipleri. Bu uygulamaların Azure AD 'de ayrıcalıklı izinleri olabilir ve kimlik doğrulama yöneticilerine başka bir yerde izin verilmez. Bu yol aracılığıyla bir kimlik doğrulama Yöneticisi bir uygulama sahibinin kimliğini varsayabilir ve sonra uygulamanın kimlik bilgilerini güncelleştirerek ayrıcalıklı bir uygulamanın kimliğini daha fazla varsayabilir.
>- Azure abonelik sahipleri, hassas veya özel bilgilere veya Azure 'da kritik yapılandırmaya erişebilir.
>- Güvenlik grubu ve Office 365 Grup sahipleri, Grup üyeliğini yönetebilir. Bu gruplar, Azure AD 'de ve başka bir yerde hassas veya özel bilgilere veya kritik yapılandırmaya erişim verebilir.
>- Exchange Online, Office Security ve Uyumluluk Merkezi ve insan kaynakları sistemleri gibi Azure AD dışında diğer hizmetlerde bulunan yöneticiler.
>- Gizli veya özel bilgilere erişebilen Yöneticiler, yasal Counsel ve insan kaynakları çalışanları gibi yönetici olmayanlar.

### <a name="azure-devops-administratorazure-devops-administrator-permissions"></a>[Azure DevOps Yöneticisi](#azure-devops-administrator-permissions)

Bu role sahip olan kullanıcılar, yeni Azure DevOps kuruluş oluşturmayı yapılandırılabilir bir kullanıcı veya grup kümesine kısıtlamak için Azure DevOps ilkesini yönetebilir. Bu roldeki kullanıcılar, bu ilkeyi şirketin Azure AD kuruluşu ile desteklenen herhangi bir Azure DevOps kuruluştan yönetebilir.

Tüm kurumsal Azure DevOps ilkeleri, bu roldeki kullanıcılar tarafından yönetilebilir.

### <a name="azure-information-protection-administratorazure-information-protection-administrator-permissions"></a>[Azure Information Protection Yöneticisi](#azure-information-protection-administrator-permissions)

Bu role sahip olan kullanıcılar Azure Information Protection hizmetinde tüm izinlere sahiptir. Bu rol, Azure Information Protection ilkesi için etiketlerin yapılandırılmasını, koruma şablonlarının yönetilmesini ve korumanın etkinleşmesini sağlar. Bu rol, kimlik koruma Merkezi, Privileged Identity Management, Office 365 hizmet durumunu Izleme veya Office 365 Güvenlik ve Uyumluluk Merkezi hiçbir izin vermez.

### <a name="b2c-user-flow-administratorb2c-user-flow-administrator-permissions"></a>[B2C Kullanıcı akış Yöneticisi](#b2c-user-flow-administrator-permissions)

Bu role sahip olan kullanıcılar B2C Kullanıcı Akışları oluşturabilir ve yönetebilir ("yerleşik" ilkeleri olarak da bilinir) Azure portal. Kullanıcı akışları oluşturarak veya düzenleyerek, bu kullanıcılar kullanıcı deneyiminin HTML/CSS/JavaScript içeriğini değiştirebilir, Kullanıcı akışı başına MFA gereksinimlerini değiştirebilir, belirteçteki talepleri değiştirebilir ve Kiracıdaki tüm ilkeler için oturum ayarlarını ayarlayabilir. Öte yandan, bu rol Kullanıcı verilerini gözden geçirme veya kiracı şemasına dahil olan özniteliklerde değişiklik yapma özelliğini içermez. Kimlik deneyimi çerçevesi (özel olarak da bilinir) ilkeleri üzerinde yapılan değişiklikler, bu rolün kapsamı da dışındadır.

### <a name="b2c-user-flow-attribute-administratorb2c-user-flow-attribute-administrator-permissions"></a>[B2C Kullanıcı akışı öznitelik Yöneticisi](#b2c-user-flow-attribute-administrator-permissions)

Bu role sahip kullanıcılar, Kiracıdaki tüm Kullanıcı akışları için kullanılabilir özel öznitelikler ekler veya siler. Bu nedenle, bu role sahip olan kullanıcılar Son Kullanıcı şemasına değişiklik yapabilir veya yeni öğe ekleyebilir, tüm Kullanıcı akışlarının davranışını etkileyebilir ve son kullanıcılar ve son olarak uygulamalara talepler olarak gönderilebilir. Bu rol Kullanıcı akışlarını düzenleyemez.

### <a name="b2c-ief-keyset-administratorb2c-ief-keyset-administrator-permissions"></a>[B2C ıEF anahtar kümesi Yöneticisi](#b2c-ief-keyset-administrator-permissions)

Kullanıcı belirteç şifreleme, belirteç imzaları ve talep şifreleme/şifre çözme için ilke anahtarlarını ve gizli dizileri oluşturabilir ve yönetebilir. Mevcut anahtar kapsayıcılarına yeni anahtarlar ekleyerek, bu sınırlı yönetici, var olan uygulamaları etkilemeden gizli dizileri gereken şekilde alabilir. Bu Kullanıcı, oluşturulduktan sonra bile bu parolaların tam içeriğini ve bunların sona erme tarihlerini görebilir.

> [!IMPORTANT]
> Bu hassas bir roldür. Anahtar kümesi yönetici rolü, üretim öncesi ve üretim sırasında dikkatli bir şekilde denetlenmeli ve bunlarla atanmalıdır.

### <a name="b2c-ief-policy-administratorb2c-ief-policy-administrator-permissions"></a>[B2C ıEF Ilke Yöneticisi](#b2c-ief-policy-administrator-permissions)

Bu roldeki kullanıcılar, Azure AD B2C tüm özel ilkeleri oluşturma, okuma, güncelleştirme ve silme yeteneğine sahiptir ve bu nedenle ilgili Azure AD B2C kiracısındaki kimlik deneyimi çerçevesinde tam denetime sahiptir. İlkeleri düzenleyerek, bu kullanıcı dış kimlik sağlayıcılarıyla doğrudan Federasyon oluşturabilir, Dizin şemasını değiştirebilir, kullanıcıya yönelik tüm içerikleri değiştirebilir (HTML, CSS, JavaScript), kimlik doğrulamasını tamamlamaya yönelik gereksinimleri değiştirebilir, yeni kullanıcılar oluşturabilir, tam geçişler dahil olmak üzere dış sistemlere Kullanıcı verileri ve parolalar ve telefon numaraları gibi hassas alanlar da dahil olmak üzere tüm Kullanıcı bilgilerini düzenleyin. Buna karşılık, bu rol, kiracı için şifreleme anahtarlarını değiştiremez veya kiracıda Federasyon için kullanılan gizli dizileri düzenleyemez.

> [!IMPORTANT]
> B2 ıEF Ilke Yöneticisi, üretimde kiracılar için çok sınırlı bir temelde atanması gereken son derece duyarlı bir roldür. Bu kullanıcılara göre etkinlikler, özellikle üretimde kiracılar için yakından denetlenmelidir.

### <a name="billing-administratorbilling-administrator-permissions"></a>[Faturalama Yöneticisi](#billing-administrator-permissions)

Satın alma işlemleri yapar, abonelikleri yönetir, destek biletlerini yönetir ve hizmetin sistem durumunu izler.

### <a name="cloud-application-administratorcloud-application-administrator-permissions"></a>[Bulut Uygulaması Yöneticisi](#cloud-application-administrator-permissions)

Bu roldeki kullanıcılar uygulama proxy 'si rolüyle aynı izinlere sahiptir ve uygulama ara sunucusunu yönetme imkanını dışlar. Bu rol, kurumsal uygulamaların ve uygulama kayıtlarının tüm yönlerini oluşturma ve yönetme olanağı verir. Bu rol Ayrıca, Microsoft Graph ve Azure AD grafiğini dışlayarak uygulama izinleri için izin verme izni verir. Bu role atanan kullanıcılar, yeni uygulama kayıtları veya kurumsal uygulamalar oluştururken sahip olarak eklenmez.

> [!IMPORTANT]
> Bu rol, uygulama kimlik bilgilerini yönetme olanağı verir. Bu role atanan kullanıcılar, bir uygulamaya kimlik bilgileri ekleyebilir ve uygulamanın kimliğini taklit etmek için bu kimlik bilgilerini kullanabilir. Uygulamanın kimliğine Kullanıcı veya başka nesneler oluşturma veya güncelleştirme yeteneği gibi Azure Active Directory erişimi verildiyse, bu role atanan bir Kullanıcı, uygulamayı taklit ederken bu eylemleri gerçekleştirebilir. Uygulamanın kimliğini taklit etme özelliği, kullanıcının Azure AD 'de rol atamaları aracılığıyla yapabilecekleri ayrıcalık yükselmesine neden olabilir. Bir kullanıcıyı bulut uygulaması Yöneticisi rolüne atamanın, bir uygulamanın kimliğini taklit etme olanağı sağladığını anlamak önemlidir.

### <a name="cloud-device-administratorcloud-device-administrator-permissions"></a>[Bulut Cihaz Yöneticisi](#cloud-device-administrator-permissions)

Bu roldeki kullanıcılar, Azure AD 'de cihazları etkinleştirebilir, devre dışı bırakabilir ve silebilir ve Azure portal Windows 10 BitLocker anahtarlarını (varsa) okuyabilir. Rol, cihazdaki diğer özellikleri yönetmek için izinler vermez.

### <a name="compliance-administratorcompliance-administrator-permissions"></a>[Uyumluluk Yöneticisi](#compliance-administrator-permissions)

Bu role sahip olan kullanıcılar, Uyumluluk Merkezi 'nde, Microsoft 365 Yönetim Merkezi, Azure ve Office 365 Güvenlik ve Uyumluluk Merkezi Microsoft 365 uyumlulukla ilgili özellikleri yönetme iznine sahiptir. Atanan, Exchange Yönetim Merkezi ve takımlar & Skype Kurumsal Yönetici merkezlerinin içindeki tüm özellikleri yönetebilir ve Azure ve Microsoft 365 için destek biletleri oluşturabilir. [Office 365 Yönetici rolleri hakkında](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)daha fazla bilgi bulabilirsiniz.

İçinde | Yapılabilir
----- | ----------
[Uyumluluk Merkezi Microsoft 365](https://protection.office.com) | Kuruluşunuzun verilerini Microsoft 365 hizmetleri arasında koruyun ve yönetin<br>Uyumluluk uyarılarını yönetme
[Uyumluluk Yöneticisi](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | Kuruluşunuzun yasal uyumluluk etkinliklerini izleyin, atayın ve doğrulayın
[Office 365 Güvenlik ve Uyumluluk Merkezi](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Veri idare yönetimi<br>Yasal ve veri araştırmasını gerçekleştirme<br>Veri konu Isteğini yönetme<br><br>Bu rol, Office 365 ' deki [Uyumluluk Yöneticisi RoleGroup](https://docs.microsoft.com/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center#permissions-needed-to-use-features-in-the-security--compliance-center) ile aynı izinlere sahiptir güvenlik ve Uyumluluk Merkezi rol tabanlı erişim denetimi.
[Intune](https://docs.microsoft.com/intune/role-based-access-control) | Tüm Intune denetim verilerini görüntüleyin
[Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Salt okuma izinlerine sahiptir ve uyarıları yönetebilir<br>Dosya ilkeleri oluşturabilir ve değiştirebilir ve dosya idare eylemlerine izin verebilir<br>Tüm yerleşik raporları Veri Yönetimi altına görüntüleyebilir

### <a name="compliance-data-administratorcompliance-data-administrator-permissions"></a>[Uyumluluk verileri Yöneticisi](#compliance-data-administrator-permissions)

Bu role sahip olan kullanıcılar, Microsoft 365 Uyumluluk Merkezi, Microsoft 365 Yönetim Merkezi ve Azure 'da verileri izleme iznine sahiptir. Kullanıcılar ayrıca Exchange Yönetim Merkezi, uyumluluk Yöneticisi ve takımlar & Skype Kurumsal Yönetim Merkezi 'nde uyumluluk verilerini izleyebilir ve Azure ve Microsoft 365 için destek biletleri oluşturabilir.

İçinde | Yapılabilir
----- | ----------
[Uyumluluk Merkezi Microsoft 365](https://protection.office.com) | Microsoft 365 hizmetleri genelinde uyumlulukla ilgili ilkeleri izleme<br>Uyumluluk uyarılarını yönetme
[Uyumluluk Yöneticisi](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | Kuruluşunuzun yasal uyumluluk etkinliklerini izleyin, atayın ve doğrulayın
[Office 365 Güvenlik ve Uyumluluk Merkezi](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Veri idare yönetimi<br>Yasal ve veri araştırmasını gerçekleştirme<br>Veri konu Isteğini yönetme<br><br>Bu rol, Office 365 ' deki [Uyumluluk verileri Yöneticisi RoleGroup](https://docs.microsoft.com/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center#permissions-needed-to-use-features-in-the-security--compliance-center) ile aynı izinlere sahiptir güvenlik ve Uyumluluk Merkezi rol tabanlı erişim denetimi.
[Intune](https://docs.microsoft.com/intune/role-based-access-control) | Tüm Intune denetim verilerini görüntüleyin
[Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Salt okuma izinlerine sahiptir ve uyarıları yönetebilir<br>Dosya ilkeleri oluşturabilir ve değiştirebilir ve dosya idare eylemlerine izin verebilir<br>Tüm yerleşik raporları Veri Yönetimi altına görüntüleyebilir

### <a name="conditional-access-administratorconditional-access-administrator-permissions"></a>[Koşullu Erişim Yöneticisi](#conditional-access-administrator-permissions)

Bu role sahip olan kullanıcılar Azure Active Directory Koşullu erişim ayarlarını yönetebilir.
> [!NOTE]
> Azure 'da Exchange ActiveSync koşullu erişim ilkesini dağıtmak için kullanıcının da genel yönetici olması gerekir.

### <a name="customer-lockbox-access-approvercustomer-lockbox-access-approver-permissions"></a>[Müşteri Kasası erişimi onaylayan](#customer-lockbox-access-approver-permissions)

Kuruluşunuzdaki [müşteri kasası isteklerini](https://docs.microsoft.com/office365/admin/manage/customer-lockbox-requests) yönetir. Müşteri Kasası istekler için e-posta bildirimleri alırlar ve Microsoft 365 Yönetim merkezinden gelen istekleri onaylayabilir ve reddedebilirler. Ayrıca Müşteri Kasası özelliğini açabilir veya kapatabilir. Yalnızca genel Yöneticiler, bu role atanan kişilerin parolalarını sıfırlayabilir.

### <a name="desktop-analytics-administratordesktop-analytics-administrator-permissions"></a>[Masaüstü Analizi Yöneticisi](#desktop-analytics-administrator-permissions)


Bu roldeki kullanıcılar masaüstü Analizi ve Office özelleştirmesi & Ilkesi hizmetlerini yönetebilir. Bu, masaüstü analizi için varlık envanterini görüntüleme, dağıtım planları oluşturma, dağıtım ve sistem durumunu görüntüleme imkanını içerir. Office özelleştirmesi & Ilke hizmetinde, bu rol kullanıcıların Office ilkelerini yönetmesine olanak sağlar.

### <a name="device-administratordevice-administrators-permissions"></a>[Cihaz Yöneticisi](#device-administrators-permissions)

Bu rol, yalnızca [cihaz ayarları](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/DeviceSettings/menuId/)'nda ek bir yerel yönetici olarak atanmak üzere kullanılabilir. Bu role sahip kullanıcılar, Azure Active Directory katılmış tüm Windows 10 cihazlarında yerel makine yöneticileri haline gelir. Azure Active Directory cihaz nesnelerini yönetme yeteneğine sahip değildir.

### <a name="directory-readersdirectory-readers-permissions"></a>[Dizin okuyucuları](#directory-readers-permissions)

Bu roldeki kullanıcılar, temel dizin bilgilerini okuyabilir. Bu rol için kullanılmalıdır:
* Belirli bir konuk kullanıcıları kümesine, tüm konuk kullanıcılara vermek yerine okuma erişimi verme.
* "Azure AD portalına erişimi yalnızca yöneticilerle kısıtla" seçeneği "Evet" olarak ayarlandığında, yönetici olmayan kullanıcıların belirli bir kümesini Azure portalına verme.
* Dizin. Read. All 'un bir seçenek olmadığı dizine hizmet sorumlusu erişimi veriliyor.

### <a name="directory-synchronization-accountsdirectory-synchronization-accounts-permissions"></a>[Dizin eşitleme hesapları](#directory-synchronization-accounts-permissions)

Kullanmayın. Bu rol, Azure AD Connect hizmetine otomatik olarak atanır ve başka bir kullanım için tasarlanmamıştır veya desteklenmez.

### <a name="directory-writersdirectory-writers-permissions"></a>[Dizin yazarları](#directory-writers-permissions)

Bu, [onay çerçevesini](../develop/quickstart-v1-integrate-apps-with-azure-ad.md)desteklemeyen uygulamalara atanacak eski bir roldür. Herhangi bir kullanıcıya atanmamalıdır.

### <a name="dynamics-365-administrator--crm-administratorcrm-service-administrator-permissions"></a>[Dynamics 365 Yöneticisi/CRM Yöneticisi](#crm-service-administrator-permissions)

Bu role sahip olan kullanıcılar, hizmet mevcut olduğunda Microsoft Dynamics 365 Online içinde genel izinlere sahiptir ve destek biletlerini yönetebilir ve hizmet durumunu izleyebilir. Daha fazla bilgi [, kiracınızı yönetmek için hizmet yöneticisi rolünü kullanın](https://docs.microsoft.com/dynamics365/customer-engagement/admin/use-service-admin-role-manage-tenant).

> [!NOTE]
> Microsoft Graph API, Azure AD Graph API ve Azure AD PowerShell 'de, bu rol "Dynamics 365 hizmet yöneticisi" olarak tanımlanır. [Azure Portal](https://portal.azure.com), "Dynamics 365 Yöneticisi" dir.

### <a name="exchange-administratorexchange-service-administrator-permissions"></a>[Exchange Yöneticisi](#exchange-service-administrator-permissions)

Bu role sahip olan kullanıcılar, hizmet mevcut olduğunda Microsoft Exchange Online içinde genel izinlere sahiptir. Ayrıca tüm Office 365 gruplarını oluşturup yönetebilir, destek biletlerini yönetebilir ve hizmet durumunu izleyebilir. [Office 365 Yönetici rolleri hakkında](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)daha fazla bilgi.

> [!NOTE]
> Microsoft Graph API, Azure AD Graph API ve Azure AD PowerShell 'de, bu rol "Exchange hizmet yöneticisi" olarak tanımlanır. [Azure Portal](https://portal.azure.com), "Exchange Yöneticisi" dir. [Exchange Yönetim Merkezi](https://go.microsoft.com/fwlink/p/?LinkID=529144)'Nde "Exchange Online Yöneticisi" dir.

### <a name="external-identity-provider-administratorexternal-identity-provider-administrator-permissions"></a>[Dış kimlik sağlayıcısı Yöneticisi](#external-identity-provider-administrator-permissions)

Bu yönetici Azure Active Directory kiracılar ve dış kimlik sağlayıcıları arasında Federasyonu yönetir. Bu rolle, kullanıcılar yeni kimlik sağlayıcıları ekleyebilir ve tüm kullanılabilir ayarları (örneğin, kimlik doğrulama yolu, hizmet KIMLIĞI, atanan anahtar kapsayıcıları) yapılandırabilir. Bu Kullanıcı, kiracının dış kimlik sağlayıcılarından kimlik doğrulama güvenmesini sağlayabilir. Son Kullanıcı deneyimlerinde ortaya çıkan etki, kiracının türüne bağlıdır:

* Çalışanlar ve iş ortakları için kiracılar Azure Active Directory: bir Federasyonun eklenmesi (örn. Gmail ile), henüz kullanılmadı tüm konuk davetlerini hemen etkiler. Bkz. [B2B Konuk kullanıcıları için kimlik sağlayıcısı olarak Google ekleme](https://docs.microsoft.com/azure/active-directory/b2b/google-federation).
* Azure Active Directory B2C kiracılar: bir Federasyonun eklenmesi (örneğin, Facebook ile veya başka bir Azure AD kuruluşunda), kimlik sağlayıcısı Kullanıcı akışında bir seçenek olarak eklenene kadar Son Kullanıcı akışlarını hemen etkilemez (yerleşik olarak da adlandırılır). ilke). Bir örnek için [kimlik sağlayıcısı olarak Microsoft hesabı yapılandırma](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-msa-app) konusuna bakın. Kullanıcı akışlarını değiştirmek için, "B2C Kullanıcı akış Yöneticisi" nin sınırlı rolü gereklidir.

### <a name="global-administrator--company-administratorcompany-administrator-permissions"></a>[Genel yönetici/Şirket Yöneticisi](#company-administrator-permissions)

Bu role sahip olan kullanıcılar, Azure Active Directory ' deki tüm yönetim özelliklerine ve Azure Active Directory ayrıca Microsoft 365 Güvenlik Merkezi, Microsoft 365 Uyumluluk Merkezi, Exchange Online, SharePoint Online ve Skype Kurumsal Çevrimiçi Azure Active Directory kiracıya kaydolan kişi genel yönetici olur. Yalnızca genel Yöneticiler, diğer yönetici rollerini atayabilir. Şirketinizde birden fazla genel yönetici olabilir. Genel Yöneticiler, tüm kullanıcılar ve diğer tüm yöneticiler için parolayı sıfırlayabilir.

> [!NOTE]
> Microsoft Graph API, Azure AD Graph API ve Azure AD PowerShell 'de, bu rol "Şirket Yöneticisi" olarak tanımlanır. [Azure Portal](https://portal.azure.com), "genel yönetici" dir.
>
>

### <a name="global-readerglobal-reader-permissions"></a>[Genel okuyucu](#global-reader-permissions)

Bu roldeki kullanıcılar Microsoft 365 hizmetleri genelinde ayarları ve yönetim bilgilerini okuyabilir, ancak yönetim eylemleri alamaz. Genel okuyucu genel yöneticiye salt okunurdur. Planlama, denetim veya araştırmalar için genel yönetici yerine genel okuyucu atayın. Küresel bir yönetici rolü atanmadan çalışmayı kolaylaştırmak için, Exchange Yöneticisi gibi diğer sınırlı yönetici rolleriyle birlikte genel okuyucu kullanın. Küresel okuyucu Microsoft 365 Yönetim Merkezi, Exchange Yönetim Merkezi, takımlar Yönetim Merkezi, Güvenlik Merkezi, Uyumluluk Merkezi, Azure AD Yönetim Merkezi ve cihaz yönetimi Yönetim Merkezi ile birlikte kullanılabilir.

> [!NOTE]
> Genel okuyucu rolünde Şu anda birkaç kısıtlama vardır-
>
>- SharePoint Yönetim Merkezi-SharePoint Yönetim Merkezi, genel okuyucu rolünü desteklemez. [Microsoft 365 Yönetim Merkezi](https://admin.microsoft.com/Adminportal/Home#/homepage)'Nde yönetim merkezleri altında sol bölmede ' SharePoint ' öğesini görmezsiniz.
>- [OneDrive Yönetim Merkezi](https://admin.onedrive.com/) -OneDrive Yönetim Merkezi, genel okuyucu rolünü desteklemez.
>- [Azure AD Portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) -genel okuyucu, bir kurumsal uygulamanın sağlama modunu okuyamıyor.
>- [M365 Yönetim Merkezi](https://admin.microsoft.com/Adminportal/Home#/homepage) -genel okuyucu, müşteri kasası isteklerini okuyamıyor. M365 Yönetim Merkezi 'nin sol bölmesinde, **destek** altında **müşteri kasası istekleri** sekmesini bulmayacağız.
>- [M365 Güvenlik Merkezi](https://security.microsoft.com/homepage) -genel okuyucu duyarlılık ve Bekletme etiketlerini okuyamıyor. M365 Güvenlik Merkezi 'nin sol bölmesinde **duyarlık etiketleri**, **bekletme etiketleri**ve **etiket Analizi** sekmelerini bulmayacağız.
>- [Takımlar Yönetim Merkezi](https://admin.teams.microsoft.com) -küresel okuyucu **takımlar yaşam döngüsünü**, **Analiz & raporlarını**, **IP telefon cihaz yönetimini** ve **uygulama kataloğunu**okuyamıyor.
>- [Privileged Access Management (Pam)](https://docs.microsoft.com/office365/securitycompliance/privileged-access-management-overview) , genel okuyucu rolünü desteklemez.
>- [Azure Information Protection](https://docs.microsoft.com/azure/information-protection/what-is-information-protection) -genel okuyucu yalnızca [merkezi raporlama Için](https://docs.microsoft.com/azure/information-protection/reports-aip) desteklenir ve Azure AD Kuruluşunuz [Birleşik etiketleme platformunda](https://docs.microsoft.com/azure/information-protection/faqs#how-can-i-determine-if-my-tenant-is-on-the-unified-labeling-platform)ne zaman değildir.
>
> Bu özellikler Şu anda geliştirme aşamasındadır.
>

### <a name="group-administratorgroup-administrator"></a>[Grup Yöneticisi](#group-administrator)

Bu roldeki kullanıcılar, adlandırma ve süre sonu ilkeleri gibi grupları ve ayarlarını oluşturabilir/yönetebilir. Bu role bir Kullanıcı atamanın, bu kullanıcılara Kiracıdaki tüm grupları ekipler, SharePoint, Yammer gibi çeşitli iş yükleri arasında Outlook 'a ek olarak yönetme olanağı sağladığını anlamak önemlidir. Ayrıca, Kullanıcı Microsoft Yönetim Merkezi, Azure Portal gibi çeşitli yönetim portallarında ve takımlar ve SharePoint yönetim merkezleri gibi iş yüklerinin yanı sıra çeşitli gruplar ayarlarını yönetebilecektir.

### <a name="guest-inviterguest-inviter-permissions"></a>[Konuk davetci](#guest-inviter-permissions)

**Üyeler** Kullanıcı ayarı Hayır olarak ayarlandığında, bu roldeki KULLANıCıLAR Azure Active Directory B2B Konuk kullanıcı davetlerini yönetebilir. [Azure AD B2B Işbirliği hakkında](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)B2B işbirliği hakkında daha fazla bilgi. Başka herhangi bir izin içermez.

### <a name="helpdesk-administratorhelpdesk-administrator-permissions"></a>[Yardım Masası Yöneticisi](#helpdesk-administrator-permissions)

Bu role sahip kullanıcılar parolaları değiştirebilir, yenileme belirteçlerini geçersiz kılabilir, hizmet isteklerini yönetebilir ve hizmet durumunu izleyebilir. Yenileme belirtecinin geçersiz kılınması, kullanıcının yeniden oturum açmasını zorlar. Yardım Masası yöneticileri parolaları sıfırlayabilir ve yönetici olmayan veya yalnızca şu rolleri atayan diğer kullanıcıların yenileme belirteçlerini geçersiz kılabilir:

* Dizin okuyucuları
* Konuk davetci
* Yardım Masası Yöneticisi
* İleti Merkezi okuyucusu
* Rapor okuyucu

> [!IMPORTANT]
> Bu role sahip kullanıcılar, Azure Active Directory ' nin içindeki ve dışındaki gizli veya özel bilgilere veya kritik yapılandırmaya erişimi olabilecek kişilerin parolalarını değiştirebilir. Bir kullanıcının parolasını değiştirmek, kullanıcının kimliğini ve izinlerini kabul etme imkanını ifade edebilir. Örneğin:
>
>- Sahip oldukları uygulamaların kimlik bilgilerini yönetebilen uygulama kaydı ve kurumsal uygulama sahipleri. Bu uygulamaların Azure AD 'de ayrıcalıklı izinleri olabilir ve yardım masası yöneticilerine başka bir yerde izin verilmez. Bu yol aracılığıyla bir yardım masası Yöneticisi, bir uygulama sahibinin kimliğini varsayabilir ve daha sonra uygulamanın kimlik bilgilerini güncelleştirerek ayrıcalıklı bir uygulamanın kimliğini daha fazla varsayabilir.
>- Azure abonelik sahipleri, hassas veya özel bilgilere veya Azure 'da kritik yapılandırmaya erişebilir.
>- Güvenlik grubu ve Office 365 Grup sahipleri, Grup üyeliğini yönetebilir. Bu gruplar, Azure AD 'de ve başka bir yerde hassas veya özel bilgilere veya kritik yapılandırmaya erişim verebilir.
>- Exchange Online, Office Security ve Uyumluluk Merkezi ve insan kaynakları sistemleri gibi Azure AD dışında diğer hizmetlerde bulunan yöneticiler.
>- Gizli veya özel bilgilere erişebilen Yöneticiler, yasal Counsel ve insan kaynakları çalışanları gibi yönetici olmayanlar.

Kullanıcıların alt kümeleri üzerinde yönetim izinlerinin yetkisini verme ve Kullanıcı alt kümesine ilke uygulama, [yönetim birimleri (şimdi genel önizlemede)](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-administrative-units)olabilir.

Bu rol daha önce [Azure Portal](https://portal.azure.com/)"parola Yöneticisi" olarak adlandırılmıştı. Azure AD 'deki "Yardım Masası Yöneticisi" adı artık Azure AD PowerShell, Azure AD Graph API ve Microsoft Graph API 'sindeki adıyla eşleşiyor.

### <a name="intune-administratorintune-service-administrator-permissions"></a>[Intune Yöneticisi](#intune-service-administrator-permissions)

Bu role sahip olan kullanıcılar, hizmet mevcut olduğunda Microsoft Intune çevrimiçi olarak genel izinlere sahiptir. Ayrıca, bu rol, ilkeyi ilişkilendirmek ve grupları oluşturmak ve yönetmek için kullanıcıları ve cihazları yönetme özelliğini içerir. [Microsoft Intune Ile rol tabanlı yönetim denetimi (RBAC)](https://docs.microsoft.com/intune/role-based-access-control) hakkında daha fazla bilgi

> [!NOTE]
> Microsoft Graph API, Azure AD Graph API ve Azure AD PowerShell 'de, bu rol "Intune Hizmet Yöneticisi" olarak tanımlanır. Bu, [Azure Portal](https://portal.azure.com)"Intune Yöneticisi" dir.

### <a name="kaizala-administratorkaizala-administrator-permissions"></a>[Kaizala Yöneticisi](#kaizala-administrator-permissions)

Bu role sahip olan kullanıcılar, hizmet mevcut olduğunda Microsoft Kaizala içindeki ayarları yönetmek için genel izinlere sahiptir ve destek biletlerini yönetebilir ve hizmet durumunu izleyebilir. Ayrıca, Kullanıcı, kaizala tarafından Kaizala tarafından kullanılan ve Kaizala eylemleri kullanılarak oluşturulan iş raporlarının kullanımına & benimseme ile ilgili raporlara erişebilir.

### <a name="license-administratorlicense-administrator-permissions"></a>[Lisans Yöneticisi](#license-administrator-permissions)

Bu roldeki kullanıcılar, kullanıcılar, gruplar (grup tabanlı lisanslama kullanarak) için lisans atamaları ekleyebilir, kaldırabilir ve güncelleştirebilir ve kullanıcılara kullanım konumunu yönetebilir. Rol, abonelik satın alma veya yönetme, Grup oluşturma veya yönetme veya kullanım konumunun ötesinde Kullanıcı oluşturma veya yönetme olanağı vermez. Bu rolün destek biletlerini görüntüleme, oluşturma veya yönetme erişimi yok.

### <a name="message-center-privacy-readermessage-center-privacy-reader-permissions"></a>[İleti Merkezi Gizlilik okuyucusu](#message-center-privacy-reader-permissions)

Bu roldeki kullanıcılar, veri gizlilik iletileri dahil olmak üzere Ileti merkezindeki tüm bildirimleri izleyebilir. İleti Merkezi Gizlilik okuyucuları, veri gizliliğiyle ilgili olanlar da dahil olmak üzere e-posta bildirimleri alır ve Ileti merkezi tercihlerini kullanarak aboneliği kaldırabilirsiniz Veri gizliliği iletilerini yalnızca genel yönetici ve Ileti Merkezi Gizlilik okuyucusu okuyabilir. Ayrıca, bu rol grupları, etki alanlarını ve abonelikleri görüntüleme özelliğini içerir. Bu rolün hizmet isteklerini görüntüleme, oluşturma veya yönetme izni yok.

### <a name="message-center-readermessage-center-reader-permissions"></a>[İleti Merkezi okuyucusu](#message-center-reader-permissions)

Bu roldeki kullanıcılar, Exchange, Intune ve Microsoft ekipleri gibi yapılandırılmış hizmetlerde kuruluşları için [Office 365 İleti merkezindeki](https://support.office.com/article/Message-center-in-Office-365-38FB3333-BFCC-4340-A37B-DEDA509C2093) bildirimleri ve danışmanlık sistem durumu güncelleştirmelerini izleyebilir. İleti Merkezi okuyucuları, Office 365 ' de ileti merkezi gönderilerini bir haftalık e-posta ile alırlar. Azure AD 'de, bu role atanan kullanıcılar yalnızca kullanıcılar ve gruplar gibi Azure AD hizmetlerinde salt okuma erişimine sahip olur. Bu rolün destek biletlerini görüntüleme, oluşturma veya yönetme erişimi yok.

### <a name="office-apps-administratoroffice-apps-administrator-permissions"></a>[Office uygulamaları Yöneticisi](#office-apps-administrator-permissions)

Bu roldeki kullanıcılar, Office 365 uygulamalarının bulut ayarlarını yönetebilir. Bu, bulut ilkelerinin yönetimi, self servis indirme yönetimi ve Office uygulamalarıyla ilgili raporu görüntüleme imkanını içerir. Bu rol Ayrıca, destek biletlerini yönetme ve ana yönetim merkezinde hizmet durumunu izleme özelliğini sağlar. Bu role atanan kullanıcılar, Office uygulamalarındaki yeni özelliklerin iletişimini de yönetebilir. 

### <a name="partner-tier1-supportpartner-tier1-support-permissions"></a>[Partner Katman1 desteği](#partner-tier1-support-permissions)

Kullanmayın. Bu rol kullanımdan kaldırılmıştır ve gelecekte Azure AD 'den kaldırılacak. Bu rol, az sayıda Microsoft yeniden iş ortağı tarafından kullanılmaya yöneliktir ve genel kullanım için tasarlanmamıştır.

### <a name="partner-tier2-supportpartner-tier2-support-permissions"></a>[Partner Katman2 desteği](#partner-tier2-support-permissions)

Kullanmayın. Bu rol kullanımdan kaldırılmıştır ve gelecekte Azure AD 'den kaldırılacak. Bu rol, az sayıda Microsoft yeniden iş ortağı tarafından kullanılmaya yöneliktir ve genel kullanım için tasarlanmamıştır.

### <a name="password-administratorpassword-administrator-permissions"></a>[Parola Yöneticisi](#password-administrator-permissions)

Bu role sahip olan kullanıcılar, parolaları yönetme yeteneğine sahiptir. Bu rol, hizmet isteklerini yönetme veya hizmet durumunu izleme özelliği vermez. Parola yöneticileri yönetici olmayan veya yalnızca aşağıdaki rollerin üyesi olan diğer kullanıcıların parolalarını sıfırlayabilir:

* Dizin okuyucuları
* Konuk davetci
* Parola Yöneticisi

### <a name="power-bi-administratorpower-bi-service-administrator-permissions"></a>[Power BI Yöneticisi](#power-bi-service-administrator-permissions)

Bu role sahip olan kullanıcılar Microsoft Power BI içinde genel izinlere sahiptir, hizmet mevcut olduğunda, destek biletlerini yönetebilir ve hizmet durumunu izleyebilir. [Power BI yönetici rolünü anlama](https://docs.microsoft.com/power-bi/service-admin-role)hakkında daha fazla bilgi.

> [!NOTE]
> Microsoft Graph API, Azure AD Graph API ve Azure AD PowerShell 'de, bu rol "Power BI Hizmet Yöneticisi" olarak tanımlanır. Bu, [Azure Portal](https://portal.azure.com)"Power BI Yöneticisi" dir.

### <a name="privileged-authentication-administratorprivileged-authentication-administrator-permissions"></a>[Ayrıcalıklı kimlik doğrulama Yöneticisi](#privileged-authentication-administrator-permissions)

Bu role sahip kullanıcılar, genel yöneticiler dahil olmak üzere tüm kullanıcılar için parola olmayan kimlik bilgilerini ayarlayabilir veya sıfırlayabilir ve tüm kullanıcılar için parolaları güncelleştirebilirler. Ayrıcalıklı kimlik doğrulama yöneticileri, kullanıcıları mevcut parola olmayan kimlik bilgisine (örn. MFA, FIDO) yeniden kaydetmeye zorlayabilir ve ' cihazda MFA 'yı anımsa ' seçeneğini iptal edebilir ve tüm kullanıcıların bir sonraki oturumunda MFA için istemde bulunabilir.

### <a name="privileged-role-administratorprivileged-role-administrator-permissions"></a>[Ayrıcalıklı rol yöneticisi](#privileged-role-administrator-permissions)

Bu role sahip kullanıcılar, Azure Active Directory ve Azure AD Privileged Identity Management içindeki rol atamalarını yönetebilir. Ayrıca, bu rol Privileged Identity Management ve yönetim birimlerinin tüm yönlerinin yönetimine izin verir.

> [!IMPORTANT]
> Bu rol, genel yönetici rolü dahil olmak üzere tüm Azure AD rolleri için atamaları yönetme olanağı verir. Bu rol, Azure AD 'de Kullanıcı oluşturma veya güncelleştirme gibi başka ayrıcalıklı yetenekler içermez. Ancak, bu role atanan kullanıcılar, ek roller atayarak kendilerine veya başkalarına ek ayrıcalık verebilir.

### <a name="reports-readerreports-reader-permissions"></a>[Rapor okuyucu](#reports-reader-permissions)

Bu role sahip kullanıcılar, Microsoft 365 Yönetim Merkezi 'nde ve Power BI ' de benimseme bağlam paketindeki kullanım raporlama verilerini ve raporlar panosunu görüntüleyebilir. Rol Ayrıca, Azure AD 'de oturum açma raporlarına ve etkinliklere ve Microsoft Graph Raporlama API 'SI tarafından döndürülen verilere erişim sağlar. Rapor okuyucu rolüne atanan bir Kullanıcı yalnızca ilgili kullanım ve benimseme ölçümlerine erişebilir. Ayarları yapılandırmak veya Exchange gibi ürüne özel yönetim merkezlerine erişmek için yönetici izinleri yoktur. Bu rolün destek biletlerini görüntüleme, oluşturma veya yönetme erişimi yok.

### <a name="search-administratorsearch-administrator-permissions"></a>[Yönetici ara](#search-administrator-permissions)

Bu roldeki kullanıcılar, Microsoft 365 Yönetim Merkezi 'ndeki tüm Microsoft arama yönetimi özelliklerine tam erişime sahiptir. Arama yöneticileri, kullanıcılara arama yöneticileri ve arama Düzenleyicisi rollerini devredebilir ve yer işaretleri, soru & olarak ve konumlar gibi içerik oluşturabilir ve yönetebilir. Ayrıca, bu kullanıcılar ileti merkezini görüntüleyebilir, hizmet durumunu izleyebilir ve hizmet istekleri oluşturabilir.

### <a name="search-editorsearch-editor-permissions"></a>[Arama Düzenleyicisi](#search-editor-permissions)

Bu roldeki kullanıcılar, Microsoft 365 Yönetim Merkezi 'nde yer işaretleri, Q & as ve konumlar dahil Microsoft Search için içerik oluşturabilir, yönetebilir ve silebilir.

### <a name="security-administratorsecurity-administrator-permissions"></a>[Güvenlik Yöneticisi](#security-administrator-permissions)

Bu role sahip olan kullanıcılar, Microsoft 365 Güvenlik Merkezi, Azure Active Directory Kimlik Koruması, Azure Information Protection ve Office 365 Güvenlik ve Uyumluluk Merkezi içindeki güvenlikle ilgili özellikleri yönetme iznine sahiptir. Office 365 izinleri hakkında daha fazla bilgi [Için office 365 güvenlik ve Uyumluluk Merkezi izinleri](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1)bulabilirsiniz.

İçinde | Yapılabilir
--- | ---
[Microsoft 365 Güvenlik Merkezi](https://protection.office.com) | Microsoft 365 hizmetleri arasında güvenlikle ilgili ilkeleri izleme<br>Güvenlik tehditlerini ve uyarıları yönetme<br>Raporları görüntüle
Kimlik koruma Merkezi | Güvenlik okuyucusu rolünün tüm izinleri<br>Ayrıca, parola sıfırlama hariç tüm kimlik koruması merkezi işlemlerini gerçekleştirme özelliği
[Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | Güvenlik okuyucusu rolünün tüm izinleri<br>Azure AD rol atamaları veya **ayarları yönetemez**
[Office 365 Güvenlik ve Uyumluluk Merkezi](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Güvenlik ilkelerini yönetme<br>Güvenlik tehditlerini görüntüleyin, araştırın ve yanıtlayın<br>Raporları görüntüle
Azure Gelişmiş Tehdit Koruması | Şüpheli güvenlik etkinliğini izleyin ve yanıtlayın
Windows Defender ATP ve EDR | Rol atama<br>Makine gruplarını yönetme<br>Uç nokta tehdit algılamasını ve otomatik düzeltmeyi yapılandırma<br>Uyarıları görüntüleyin, araştırın ve yanıtlayın
[Intune](https://docs.microsoft.com/intune/role-based-access-control) | Kullanıcı, cihaz, kayıt, yapılandırma ve uygulama bilgilerini görüntüler<br>Intune üzerinde değişiklik yapılamıyor
[Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Yönetici ekleme, ilke ve ayarlar ekleme, günlükleri karşıya yükleme ve idare eylemleri gerçekleştirme
[Azure Güvenlik Merkezi](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) | Güvenlik ilkelerini görüntüleyebilir, güvenlik durumlarını görüntüleyebilir, güvenlik ilkelerini düzenleyebilir, uyarıları ve önerileri görüntüleyebilir, uyarıları ve önerileri kapatabilir
[Office 365 hizmet durumu](https://docs.microsoft.com/office365/enterprise/view-service-health) | Office 365 hizmetlerinin sistem durumunu görüntüleme

### <a name="security-operatorsecurity-operator-permissions"></a>[Güvenlik operatörü](#security-operator-permissions)

Bu role sahip kullanıcılar, uyarıları yönetebilir ve güvenlikle ilgili özellik üzerinde, Microsoft 365 Güvenlik Merkezi, Azure Active Directory, kimlik koruması Privileged Identity Management ve Office 365 ' deki tüm bilgiler dahil olmak üzere genel salt okuma erişimine sahip olabilir Güvenlik ve Uyumluluk Merkezi. Office 365 izinleri hakkında daha fazla bilgi [Için office 365 güvenlik ve Uyumluluk Merkezi izinleri](https://docs.microsoft.com/office365/securitycompliance/permissions-in-the-security-and-compliance-center)bulabilirsiniz.

İçinde | Yapılabilir
--- | ---
[Microsoft 365 Güvenlik Merkezi](https://protection.office.com) | Güvenlik okuyucusu rolünün tüm izinleri<br>Güvenlik tehditleri uyarılarını görüntüleyin, araştırın ve yanıtlayın
Kimlik koruma Merkezi | Güvenlik okuyucusu rolünün tüm izinleri<br>Ayrıca, parola sıfırlama hariç tüm kimlik koruması merkezi işlemlerini gerçekleştirme özelliği
[Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | Güvenlik okuyucusu rolünün tüm izinleri
[Office 365 Güvenlik ve Uyumluluk Merkezi](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Güvenlik okuyucusu rolünün tüm izinleri<br>Güvenlik uyarılarını görüntüleyin, araştırın ve yanıtlayın
Windows Defender ATP ve EDR | Güvenlik okuyucusu rolünün tüm izinleri<br>Güvenlik uyarılarını görüntüleyin, araştırın ve yanıtlayın
[Intune](https://docs.microsoft.com/intune/role-based-access-control) | Güvenlik okuyucusu rolünün tüm izinleri
[Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Güvenlik okuyucusu rolünün tüm izinleri
[Office 365 hizmet durumu](https://docs.microsoft.com/office365/enterprise/view-service-health) | Office 365 hizmetlerinin sistem durumunu görüntüleme

### <a name="security-readersecurity-reader-permissions"></a>[Güvenlik okuyucusu](#security-reader-permissions)

Bu role sahip olan kullanıcılar, Microsoft 365 Güvenlik Merkezi, Azure Active Directory, kimlik koruması, Privileged Identity Management ve Azure etkin okuma özellikleri dahil olmak üzere güvenlikle ilgili özellik üzerinde genel salt okuma erişimine sahiptir Dizin oturum açma raporları ve denetim günlükleri ve Office 365 Güvenlik ve Uyumluluk Merkezi. Office 365 izinleri hakkında daha fazla bilgi [Için office 365 güvenlik ve Uyumluluk Merkezi izinleri](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1)bulabilirsiniz.

İçinde | Yapılabilir
--- | ---
[Microsoft 365 Güvenlik Merkezi](https://protection.office.com) | Microsoft 365 hizmetleri arasında güvenlikle ilgili ilkeleri görüntüleme<br>Güvenlik tehditlerini ve uyarılarını görüntüleme<br>Raporları görüntüle
Kimlik koruma Merkezi | Güvenlik özellikleri için tüm güvenlik raporlarını ve ayar bilgilerini okuyun<br><ul><li>İstenmeyen postadan koruma<li>Şifreleme<li>Veri kaybını önleme<li>Kötü amaçlı yazılımdan koruma<li>Gelişmiş tehdit koruması<li>Kimlik avlayan koruma<li>Mailflow kuralları
[Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | Azure AD Privileged Identity Management ' de ortaya çıkacak tüm bilgilere salt okuma erişimi vardır: Azure AD rol atamaları ve güvenlik incelemeleri için Ilkeler ve raporlar.<br>Azure AD Privileged Identity Management için **kaydolabilir veya** herhangi bir değişiklik yapabilirsiniz. Privileged Identity Management portalında veya PowerShell aracılığıyla, bu roldeki birisi, Kullanıcı için uygun olması durumunda ek rolleri (örneğin, genel yönetici veya ayrıcalıklı rol yöneticisi) etkinleştirebilir.
[Office 365 Güvenlik ve Uyumluluk Merkezi](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Güvenlik ilkelerini görüntüleme<br>Güvenlik tehditlerini görüntüle ve araştır<br>Raporları görüntüle
Windows Defender ATP ve EDR | Uyarıları görüntüleyin ve araştırın. Windows Defender ATP 'de rol tabanlı erişim denetimini açtığınızda, Azure AD güvenlik okuyucusu rolü gibi salt okuma izinlerine sahip kullanıcılar bir Windows Defender ATP rolüne atanana kadar erişimi kaybeder.
[Intune](https://docs.microsoft.com/intune/role-based-access-control) | Kullanıcı, cihaz, kayıt, yapılandırma ve uygulama bilgilerini görüntüler. Intune üzerinde değişiklik yapılamıyor.
[Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Salt okuma izinlerine sahiptir ve uyarıları yönetebilir
[Azure Güvenlik Merkezi](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) | Önerileri ve uyarıları görüntüleyebilir, güvenlik ilkelerini görüntüleyebilir, güvenlik durumlarını görüntüleyebilir, ancak değişiklik yapamaz
[Office 365 hizmet durumu](https://docs.microsoft.com/office365/enterprise/view-service-health) | Office 365 hizmetlerinin sistem durumunu görüntüleme

### <a name="service-support-administratorservice-support-administrator-permissions"></a>[Hizmet desteği Yöneticisi](#service-support-administrator-permissions)

Bu role sahip olan kullanıcılar, Azure ve Office 365 hizmetleri için Microsoft ile destek istekleri açabilir ve [Azure Portal](https://portal.azure.com) ve [Microsoft 365 Yönetim Merkezi](https://admin.microsoft.com)'nde hizmet panosu ve ileti merkezini görüntüler. [Yönetici rolleri hakkında](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)daha fazla bilgi.

> [!NOTE]
> Microsoft Graph API, Azure AD Graph API ve Azure AD PowerShell 'de, bu rol "hizmet desteği Yöneticisi" olarak tanımlanır. Bu, [Azure Portal](https://portal.azure.com), [Microsoft 365 Yönetim Merkezi](https://admin.microsoft.com)ve Intune portalında "Hizmet Yöneticisi" dir.

### <a name="sharepoint-administratorsharepoint-service-administrator-permissions"></a>[SharePoint Yöneticisi](#sharepoint-service-administrator-permissions)

Bu role sahip olan kullanıcılar Microsoft SharePoint Online içinde genel izinlere sahiptir, hizmet mevcut olduğunda tüm Office 365 gruplarını oluşturup yönetebilir, destek biletlerini yönetebilir ve hizmet durumunu izleyebilir. [Yönetici rolleri hakkında](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)daha fazla bilgi.

> [!NOTE]
> Microsoft Graph API, Azure AD Graph API ve Azure AD PowerShell 'de, bu rol "SharePoint Hizmet Yöneticisi" olarak tanımlanır. Bu, [Azure Portal](https://portal.azure.com)"SharePoint Yöneticisi" dir.

### <a name="skype-for-business--lync-administratorlync-service-administrator-permissions"></a>[Skype Kurumsal/Lync Yöneticisi](#lync-service-administrator-permissions)

Bu role sahip olan kullanıcılar, hizmet mevcut olduğunda, Microsoft Skype Kurumsal içinde genel izinlere sahiptir ve ayrıca Azure Active Directory Skype 'e özgü kullanıcı özniteliklerini yönetir. Ayrıca, bu rol, destek biletlerini yönetme ve hizmet durumunu izleme ve takımlar ve Skype Kurumsal yönetim merkezine erişme olanağı verir. Hesap Ayrıca takımlar için lisanslanmış olmalıdır veya takımlar PowerShell cmdlet 'lerini çalıştıramıyor. Skype Kurumsal [yönetici rolü](https://support.office.com/article/about-the-skype-for-business-admin-role-aeb35bda-93fc-49b1-ac2c-c74fbeb737b5) ve [Skype Kurumsal ve Microsoft ekipleri eklenti lisanslaması](https://docs.microsoft.com/skypeforbusiness/skype-for-business-and-microsoft-teams-add-on-licensing/skype-for-business-and-microsoft-teams-add-on-licensing) hakkında daha fazla bilgi

> [!NOTE]
> Microsoft Graph API, Azure AD Graph API ve Azure AD PowerShell 'de, bu rol "Lync Hizmet Yöneticisi" olarak tanımlanır. Bu, [Azure Portal](https://portal.azure.com/)"Skype Kurumsal Yöneticisi" dir.

### <a name="teams-administratorteams-service-administrator-permissions"></a>[Takımlar Yöneticisi](#teams-service-administrator-permissions)

Bu roldeki kullanıcılar Microsoft ekipleri & Skype Kurumsal Yönetici Merkezi ve ilgili PowerShell modülleri aracılığıyla Microsoft ekipleri iş yükünün tüm yönlerini yönetebilir. Bu, diğer alanların yanı sıra telefon, mesajlaşma, toplantılar ve takımların kendileri ile ilgili tüm yönetim araçlarını içerir. Bu rol Ayrıca tüm Office 365 gruplarını oluşturma ve yönetme, destek biletlerini yönetme ve hizmet durumunu izleme özelliğini verir.
> [!NOTE]
> Microsoft Graph API, Azure AD Graph API ve Azure AD PowerShell 'de, bu rol "takımlar Hizmet Yöneticisi" olarak tanımlanır. Bu, [Azure Portal](https://portal.azure.com)"takımlar Yöneticisi" dir.

### <a name="teams-communications-administratorteams-communications-administrator-permissions"></a>[Takımlar Iletişim Yöneticisi](#teams-communications-administrator-permissions)

Bu roldeki kullanıcılar, Voice & telefonlarıyla ilgili Microsoft ekipleri iş yükünün yönlerini yönetebilir. Buna telefon numarası atama, sesli ve toplantı ilkelerinin yönetim araçları ve çağrı Analizi araç takımını tam erişim dahildir.

### <a name="teams-communications-support-engineerteams-communications-support-engineer-permissions"></a>[Takımlar Iletişimleri Destek Mühendisi](#teams-communications-support-engineer-permissions)

Bu roldeki kullanıcılar, Microsoft ekiplerinde Skype Kurumsal Yönetim Merkezi & Kullanıcı çağrısı sorun giderme araçlarını kullanarak Microsoft & ekiplerinde bulunan iletişim sorunlarını giderebilir. Bu roldeki kullanıcılar, dahil edilen tüm katılımcılar için tam çağrı kaydı bilgilerini görüntüleyebilir. Bu rolün destek biletlerini görüntüleme, oluşturma veya yönetme erişimi yok.

### <a name="teams-communications-support-specialistteams-communications-support-specialist-permissions"></a>[Takımlar Iletişimleri destek uzmanı](#teams-communications-support-specialist-permissions)

Bu roldeki kullanıcılar, Microsoft ekiplerinde Skype Kurumsal Yönetim Merkezi & Kullanıcı çağrısı sorun giderme araçlarını kullanarak Microsoft & ekiplerinde bulunan iletişim sorunlarını giderebilir. Bu roldeki kullanıcılar, yalnızca baktıkları belirli kullanıcı için yapılan çağrıda Kullanıcı ayrıntılarını görüntüleyebilir. Bu rolün destek biletlerini görüntüleme, oluşturma veya yönetme erişimi yok.

### <a name="user-administratoruser-administrator-permissions"></a>[Kullanıcı Yöneticisi](#user-administrator-permissions)

Bu role sahip kullanıcılar Kullanıcı oluşturabilir ve bazı kısıtlamalara sahip kullanıcıların tüm yönlerini yönetebilir (aşağıya bakın) ve parola süre sonu ilkelerini güncelleştirebilir. Ayrıca, bu role sahip kullanıcılar tüm grupları oluşturabilir ve yönetebilir. Bu rol Ayrıca Kullanıcı görünümleri oluşturma ve yönetme, destek biletlerini yönetme ve hizmet durumunu izleme özelliğini de içerir. Kullanıcı yöneticilerinin çoğu yönetici rolünde kullanıcılar için bazı kullanıcı özelliklerini yönetme izni yoktur. Bu kısıtlamanın özel durumları olan roller aşağıdaki tabloda listelenmiştir.

| | |
| --- | --- |
|Genel izinler|<p>Kullanıcı ve grup oluşturma</p><p>Kullanıcı görünümleri oluşturma ve yönetme</p><p>Office destek biletlerini yönetme<p>Parola süre sonu ilkelerini Güncelleştir|
|<p>Tüm kullanıcılar (tüm yöneticiler dahil)</p>|<p>Lisansları yönetme</p><p>Kullanıcı asıl adı dışındaki tüm kullanıcı özelliklerini yönet</p>
|Yalnızca yönetici olmayan veya aşağıdaki sınırlı Yönetici rollerinin hiçbirinde olan kullanıcılar için:<ul><li>Dizin okuyucuları<li>Konuk davetci<li>Yardım Masası Yöneticisi<li>İleti Merkezi okuyucusu<li>Rapor okuyucu<li>Kullanıcı Yöneticisi|<p>Sil ve geri yükle</p><p>Devre dışı bırak ve Etkinleştir</p><p>Yenileme belirteçlerini geçersiz kıl</p><p>Kullanıcı asıl adı dahil tüm kullanıcı özelliklerini yönet</p><p>Parola sıfırla</p><p>Güncelleştirme (FIDO) cihaz anahtarları</p>|

> [!IMPORTANT]
> Bu role sahip kullanıcılar, Azure Active Directory ' nin içindeki ve dışındaki gizli veya özel bilgilere veya kritik yapılandırmaya erişimi olabilecek kişilerin parolalarını değiştirebilir. Bir kullanıcının parolasını değiştirmek, kullanıcının kimliğini ve izinlerini kabul etme imkanını ifade edebilir. Örneğin:
>
>- Sahip oldukları uygulamaların kimlik bilgilerini yönetebilen uygulama kaydı ve kurumsal uygulama sahipleri. Bu uygulamaların Azure AD 'de ayrıcalıklı izinleri olabilir ve Kullanıcı yöneticilerine başka bir yerde izin verilmez. Bu yoldan, bir Kullanıcı Yöneticisi bir uygulama sahibinin kimliğini varsayabilir ve daha sonra uygulamanın kimlik bilgilerini güncelleştirerek ayrıcalıklı bir uygulamanın kimliğini daha fazla varsayabilir.
>- Azure abonelik sahipleri, hassas veya özel bilgilere veya Azure 'da kritik yapılandırmaya erişebilir.
>- Güvenlik grubu ve Office 365 Grup sahipleri, Grup üyeliğini yönetebilir. Bu gruplar, Azure AD 'de ve başka bir yerde hassas veya özel bilgilere veya kritik yapılandırmaya erişim verebilir.
>- Exchange Online, Office Security ve Uyumluluk Merkezi ve insan kaynakları sistemleri gibi Azure AD dışında diğer hizmetlerde bulunan yöneticiler.
>- Gizli veya özel bilgilere erişebilen Yöneticiler, yasal Counsel ve insan kaynakları çalışanları gibi yönetici olmayanlar.

## <a name="role-permissions"></a>Rol Izinleri

Aşağıdaki tablolarda, her role verilen Azure Active Directory belirli izinler açıklanır. Bazı rollerin, Microsoft hizmetlerinde Azure Active Directory dışında ek izinleri olabilir.

### <a name="application-administrator-permissions"></a>Uygulama Yöneticisi izinleri

, Uygulama kayıtlarının ve kurumsal uygulamaların tüm yönlerini oluşturabilir ve yönetebilir.

| **Eylemler** | **Açıklama** |
| --- | --- |
| Microsoft. Directory/Application/appProxyAuthentication/Update | Azure Active Directory 'de hizmet sorumluları 'nda uygulama proxy 'Si kimlik doğrulama özelliklerini güncelleştirin. |
| Microsoft. Directory/Application/appProxyUrlSettings/Update | Azure Active Directory içindeki uygulama proxy 'si iç ve dış URL 'LERI güncelleştirin. |
| Microsoft. Directory/uygulamalar/applicationProxy/okuma | Tüm uygulama proxy özelliklerini okuyun. |
| Microsoft. Directory/uygulamalar/applicationProxy/güncelleştirme | Tüm uygulama proxy özelliklerini güncelleştirin. |
| Microsoft. Directory/uygulamalar/hedef kitle/güncelleştirme | Azure Active Directory 'da Applications. Audience özelliğini güncelleştirin. |
| Microsoft. Directory/uygulamalar/kimlik doğrulaması/güncelleştirme | Azure Active Directory güncelleştirme uygulamaları. kimlik doğrulama özelliği. |
| Microsoft. Directory/uygulamalar/temel/güncelleştirme | Azure Active Directory içindeki uygulamalarda temel özellikleri güncelleştirin. |
| Microsoft. Directory/uygulamalar/oluşturma | Azure Active Directory uygulamalar oluşturun. |
| Microsoft. Directory/uygulamalar/kimlik bilgileri/güncelleştirme | Azure Active Directory içindeki Applications. Credentials özelliğini güncelleştirin. |
| Microsoft. Directory/uygulamalar/Sil | Azure Active Directory uygulamaları silin. |
| Microsoft. Directory/uygulamalar/sahipler/güncelleştirme | Azure Active Directory içindeki Applications. Owners özelliğini güncelleştirin. |
| Microsoft. Directory/uygulamalar/izinler/güncelleştirme | Azure Active Directory 'da Applications. Permissions özelliğini güncelleştirin. |
| Microsoft. Directory/uygulamalar/ilkeler/güncelleştirme | Azure Active Directory içindeki Applications. Policies özelliğini güncelleştirin. |
| Microsoft. Directory/Approtaatamalar/oluşturma | Azure Active Directory Approlet atamaları oluşturun. |
| Microsoft. Directory/Approtaatamalar/okuma | Azure Active Directory Approtaatamaları okuyun. |
| Microsoft. Directory/Approtaatamalar/güncelleştirme | Azure Active Directory Approtaatamaları güncelleştirin. |
| Microsoft. Directory/Approtaatamaları/silme | Azure Active Directory Approtaatamaları silin. |
| Microsoft. Directory/auditLogs/allProperties/Read | Azure Active Directory içindeki auditLogs 'ta tüm özellikleri (ayrıcalıklı özellikler dahil) okuyun. |
| Microsoft. Directory/connectorGroups/her şey/okuma | Azure Active Directory içindeki uygulama proxy Bağlayıcısı grubu özelliklerini okuyun. |
| Microsoft. Directory/connectorGroups/her şey/güncelleştirme | Azure Active Directory tüm uygulama proxy Bağlayıcısı grubu özelliklerini güncelleştirin. |
| Microsoft. Directory/connectorGroups/Create | Azure Active Directory 'de uygulama proxy Bağlayıcısı grupları oluşturun. |
| Microsoft. Directory/connectorGroups/Delete | Azure Active Directory içindeki uygulama proxy Bağlayıcısı gruplarını silin. |
| Microsoft. Directory/bağlayıcılar/her şey/okuma | Azure Active Directory içindeki tüm uygulama proxy Bağlayıcısı özelliklerini okuyun. |
| Microsoft. Directory/bağlayıcılar/oluşturma | Azure Active Directory içinde uygulama proxy bağlayıcıları oluşturun. |
| Microsoft. Directory/policies/applicationConfiguration/Basic/Read | Azure Active Directory içindeki policies. applicationConfiguration özelliğini okuyun. |
| Microsoft. Directory/policies/applicationConfiguration/Basic/Update | Azure Active Directory ilke. applicationConfiguration özelliğini güncelleştirin. |
| Microsoft. Directory/policies/applicationConfiguration/Create | Azure Active Directory ilke oluşturun. |
| Microsoft. Directory/policies/applicationConfiguration/Delete | Azure Active Directory ilkeleri silin. |
| Microsoft. Directory/policies/applicationConfiguration/Owners/Read | Azure Active Directory içindeki policies. applicationConfiguration özelliğini okuyun. |
| Microsoft. Directory/policies/applicationConfiguration/Owners/Update | Azure Active Directory ilke. applicationConfiguration özelliğini güncelleştirin. |
| Microsoft. Directory/policies/applicationConfiguration/policyAppliedTo/Read | Azure Active Directory içindeki policies. applicationConfiguration özelliğini okuyun. |
| Microsoft. Directory/Servicesorumlularını/appRoleAssignedTo/Update | Azure Active Directory Servicesorumlularını. appRoleAssignedTo özelliğini güncelleştirin. |
| Microsoft. Directory/Servicesorumlularını/Approtaatamalar/güncelleştirme | Azure Active Directory Servicesorumlularını. Approtaatamalar özelliğini güncelleştirin. |
| Microsoft. Directory/Servicesorumlularını/hedef kitle/güncelleştirme | Azure Active Directory Servicesorumlularını. Audience özelliğini güncelleştirin. |
| Microsoft. Directory/Servicesorumlularını/kimlik doğrulaması/güncelleştirme | Azure Active Directory Servicesorumlularını. Authentication özelliğini güncelleştirin. |
| Microsoft. Directory/Servicesorumlularını/temel/güncelleştirme | Azure Active Directory içindeki Servicesorumlularını temel özelliklerini güncelleştirin. |
| Microsoft. Directory/Servicesorumlularını/oluşturma | Azure Active Directory 'de Servicesorumlularını oluşturun. |
| Microsoft. Directory/Servicesorumlularını/kimlik bilgileri/güncelleştirme | Azure Active Directory Servicesorumlularını. Credentials özelliğini güncelleştirin. |
| Microsoft. Directory/Servicesorumlularını/silme | Azure Active Directory Servicesorumlularını silin. |
| Microsoft. Directory/Servicesorumlularını/Owners/Update | Azure Active Directory Servicesorumlularını. Owners özelliğini güncelleştirin. |
| Microsoft. Directory/Servicesorumlularını/izinleri/güncelleştirme | Azure Active Directory Servicesorumlularını. Permissions özelliğini güncelleştirin. |
| Microsoft. Directory/Servicesorumlularını/ilkeleri/güncelleştirme | Azure Active Directory Servicesorumlularını. Policies özelliğini güncelleştirin. |
| Microsoft. Directory/Signınreports/allProperties/Read | Azure Active Directory içindeki Signınreports 'ta tüm özellikleri (ayrıcalıklı özellikler dahil) okuyun. |
| Microsoft. Azure. serviceHealth/Allentitıes/allTasks | Azure hizmet durumunu okuyun ve yapılandırın. |
| Microsoft. Azure. Supportbilet/allEntities/allTasks | Azure destek biletleri oluşturun ve yönetin. |
| Microsoft. office365. serviceHealth/allEntities/allTasks | Office 365 hizmet durumunu okuyun ve yapılandırın. |
| Microsoft. office365. Supportbilet/allEntities/allTasks | Office 365 destek biletleri oluşturun ve yönetin. |

### <a name="application-developer-permissions"></a>Uygulama geliştirici izinleri

' Kullanıcılar uygulama kaydedebilir ' ayarından bağımsız olarak uygulama kayıtları oluşturabilir.

| **Eylemler** | **Açıklama** |
| --- | --- |
| Microsoft. Directory/Applications/createAsOwner | Azure Active Directory uygulamalar oluşturun. Oluşturucu ilk sahip olarak eklenir ve oluşturulan nesne, oluşturucunun 250 tarafından oluşturulan nesneler kotasına göre sayılır. |
| Microsoft. Directory/Approtaatamalar/createAsOwner | Azure Active Directory Approlet atamaları oluşturun. Oluşturucu ilk sahip olarak eklenir ve oluşturulan nesne, oluşturucunun 250 tarafından oluşturulan nesneler kotasına göre sayılır. |
| Microsoft. Directory/oAuth2PermissionGrants/createAsOwner | Azure Active Directory 'de oAuth2PermissionGrants oluşturun. Oluşturucu ilk sahip olarak eklenir ve oluşturulan nesne, oluşturucunun 250 tarafından oluşturulan nesneler kotasına göre sayılır. |
| Microsoft. Directory/Servicesorumlularını/createAsOwner | Azure Active Directory 'de Servicesorumlularını oluşturun. Oluşturucu ilk sahip olarak eklenir ve oluşturulan nesne, oluşturucunun 250 tarafından oluşturulan nesneler kotasına göre sayılır. |

### <a name="authentication-administrator-permissions"></a>Kimlik doğrulama Yöneticisi izinleri

Yönetici olmayan kullanıcılar için kimlik doğrulama yöntemi bilgilerini görüntüleme, ayarlama ve sıfırlamaya izin verildi.

| **Eylemler** | **Açıklama** |
| --- | --- |
| Microsoft. Directory/Users/ınvalidateallrefreshtokens | Azure Active Directory tüm Kullanıcı yenileme belirteçlerini geçersiz kılar. |
| Microsoft. Directory/Users/strongAuthentication/Update | MFA kimlik bilgileri gibi güçlü kimlik doğrulama özelliklerini güncelleştirin. |
| Microsoft. Azure. serviceHealth/Allentitıes/allTasks | Azure hizmet durumunu okuyun ve yapılandırın. |
| Microsoft. Azure. Supportbilet/allEntities/allTasks | Azure destek biletleri oluşturun ve yönetin. |
| Microsoft. office365. webPortal/allEntities/temel/okuma | Microsoft. office365. webPortal 'daki tüm kaynaklarda temel özellikleri okuyun. |
| Microsoft. office365. serviceHealth/allEntities/allTasks | Office 365 hizmet durumunu okuyun ve yapılandırın. |
| Microsoft. office365. Supportbilet/allEntities/allTasks | Office 365 destek biletleri oluşturun ve yönetin. |
| Microsoft. Directory/Users/Password/Update | Office 365 kuruluşundaki tüm kullanıcılar için parolaları güncelleştirin. Daha ayrıntılı bilgi için çevrimiçi belgelere bakın. |

### <a name="azure-devops-administrator-permissions"></a>Azure DevOps yönetici izinleri

, Azure DevOps kuruluş ilkesini ve ayarlarını yönetebilir.

> [!NOTE]
> Bu rol Azure Active Directory dışında ek izinlere sahiptir. Daha fazla bilgi için yukarıdaki [rol açıklaması](#azure-devops-administrator) konusuna bakın.
>
>

| **Eylemler** | **Açıklama** |
| --- | --- |
| Microsoft. Azure. devOps/allEntities/allTasks | Azure DevOps 'u okuyun ve yapılandırın. |

### <a name="azure-information-protection-administrator-permissions"></a>Yönetici izinlerini Azure Information Protection

Azure Information Protection hizmetinin tüm yönlerini yönetebilir.

> [!NOTE]
> Bu rol Azure Active Directory dışında ek izinlere sahiptir. Daha fazla bilgi için yukarıdaki [rol açıklaması](#) konusuna bakın.
>
>

| **Eylemler** | **Açıklama** |
| --- | --- |
| Microsoft. Azure. ınformationprotection/allEntities/allTasks | Azure Information Protection tüm yönlerini yönetin. |
| Microsoft. Azure. serviceHealth/Allentitıes/allTasks | Azure hizmet durumunu okuyun ve yapılandırın. |
| Microsoft. Azure. Supportbilet/allEntities/allTasks | Azure destek biletleri oluşturun ve yönetin. |
| Microsoft. office365. serviceHealth/allEntities/allTasks | Office 365 hizmet durumunu okuyun ve yapılandırın. |
| Microsoft. office365. Supportbilet/allEntities/allTasks | Office 365 destek biletleri oluşturun ve yönetin. |

### <a name="b2c-user-flow-administrator-permissions"></a>B2C Kullanıcı akış Yöneticisi izinleri

Kullanıcı akışlarının tüm yönlerini oluşturun ve yönetin.

| **Eylemler** | **Açıklama** |
| --- | --- |
| Microsoft. AAD. B2C/Userakışlar/allTasks | Azure Active Directory B2C Kullanıcı akışlarını okuyun ve yapılandırın. |

### <a name="b2c-user-flow-attribute-administrator-permissions"></a>B2C Kullanıcı akış özniteliği yönetici izinleri

Tüm Kullanıcı akışları için kullanılabilen öznitelik şemasını oluşturun ve yönetin.

| **Eylemler** | **Açıklama** |
| --- | --- |
| Microsoft. AAD. B2C/userAttributes/allTasks | Azure Active Directory B2C Kullanıcı özniteliklerini okuyun ve yapılandırın. |

### <a name="b2c-ief-keyset-administrator-permissions"></a>B2C ıEF anahtar kümesi yönetici izinleri

Kimlik deneyimi çerçevesinde Federasyon ve şifreleme için gizli dizileri yönetin.

| **Eylemler** | **Açıklama** |
| --- | --- |
| Microsoft. AAD. B2C/trustFramework/keySets/allTasks | Azure Active Directory B2C 'de anahtar kümelerini okuyun ve yapılandırın. |

### <a name="b2c-ief-policy-administrator-permissions"></a>B2C ıEF Ilkesi yönetici izinleri

Kimlik deneyimi çerçevesinde güven çerçevesi ilkeleri oluşturun ve yönetin.

| **Eylemler** | **Açıklama** |
| --- | --- |
| Microsoft. AAD. B2C/trustFramework/policies/allTasks | Azure Active Directory B2C özel ilkeleri okuyun ve yapılandırın. |

### <a name="billing-administrator-permissions"></a>Faturalandırma Yöneticisi izinleri

, Ödeme bilgilerini güncelleştirme gibi genel faturalandırma ile ilgili görevleri gerçekleştirebilir.

> [!NOTE]
> Bu rol Azure Active Directory dışında ek izinlere sahiptir. Daha fazla bilgi için yukarıdaki rol açıklaması konusuna bakın.
>
>

| **Eylemler** | **Açıklama** |
| --- | --- |
| Microsoft. Directory/kuruluş/temel/güncelleştirme | Azure Active Directory 'da kuruluştaki temel özellikleri güncelleştirin. |
| Microsoft. Azure. serviceHealth/Allentitıes/allTasks | Azure hizmet durumunu okuyun ve yapılandırın. |
| Microsoft. Azure. Supportbilet/allEntities/allTasks | Azure destek biletleri oluşturun ve yönetin. |
| Microsoft. Commerce. faturalandırma/allEntities/allTasks | Office 365 faturalandırma 'nin tüm yönlerini yönetin. |
| Microsoft. office365. webPortal/allEntities/temel/okuma | Microsoft. office365. webPortal 'daki tüm kaynaklarda temel özellikleri okuyun. |
| Microsoft. office365. serviceHealth/allEntities/allTasks | Office 365 hizmet durumunu okuyun ve yapılandırın. |
| Microsoft. office365. Supportbilet/allEntities/allTasks | Office 365 destek biletleri oluşturun ve yönetin. |

### <a name="cloud-application-administrator-permissions"></a>Bulut uygulaması yönetici izinleri

Uygulama kayıtları ve kurumsal uygulamaların uygulama proxy 'Si hariç tüm yönlerini oluşturabilir ve yönetebilir.

| **Eylemler** | **Açıklama** |
| --- | --- |
| Microsoft. Directory/uygulamalar/hedef kitle/güncelleştirme | Azure Active Directory 'da Applications. Audience özelliğini güncelleştirin. |
| Microsoft. Directory/uygulamalar/kimlik doğrulaması/güncelleştirme | Azure Active Directory güncelleştirme uygulamaları. kimlik doğrulama özelliği. |
| Microsoft. Directory/uygulamalar/temel/güncelleştirme | Azure Active Directory içindeki uygulamalarda temel özellikleri güncelleştirin. |
| Microsoft. Directory/uygulamalar/oluşturma | Azure Active Directory uygulamalar oluşturun. |
| Microsoft. Directory/uygulamalar/kimlik bilgileri/güncelleştirme | Azure Active Directory içindeki Applications. Credentials özelliğini güncelleştirin. |
| Microsoft. Directory/uygulamalar/Sil | Azure Active Directory uygulamaları silin. |
| Microsoft. Directory/uygulamalar/sahipler/güncelleştirme | Azure Active Directory içindeki Applications. Owners özelliğini güncelleştirin. |
| Microsoft. Directory/uygulamalar/izinler/güncelleştirme | Azure Active Directory 'da Applications. Permissions özelliğini güncelleştirin. |
| Microsoft. Directory/uygulamalar/ilkeler/güncelleştirme | Azure Active Directory içindeki Applications. Policies özelliğini güncelleştirin. |
| Microsoft. Directory/Approtaatamalar/oluşturma | Azure Active Directory Approlet atamaları oluşturun. |
| Microsoft. Directory/Approtaatamalar/güncelleştirme | Azure Active Directory Approtaatamaları güncelleştirin. |
| Microsoft. Directory/Approtaatamaları/silme | Azure Active Directory Approtaatamaları silin. |
| Microsoft. Directory/auditLogs/allProperties/Read | Azure Active Directory içindeki auditLogs 'ta tüm özellikleri (ayrıcalıklı özellikler dahil) okuyun. |
| Microsoft. Directory/policies/applicationConfiguration/Create | Azure Active Directory ilke oluşturun. |
| Microsoft. Directory/policies/applicationConfiguration/Basic/Read | Azure Active Directory içindeki policies. applicationConfiguration özelliğini okuyun. |
| Microsoft. Directory/policies/applicationConfiguration/Basic/Update | Azure Active Directory ilke. applicationConfiguration özelliğini güncelleştirin. |
| Microsoft. Directory/policies/applicationConfiguration/Delete | Azure Active Directory ilkeleri silin. |
| Microsoft. Directory/policies/applicationConfiguration/Owners/Read | Azure Active Directory içindeki policies. applicationConfiguration özelliğini okuyun. |
| Microsoft. Directory/policies/applicationConfiguration/Owners/Update | Azure Active Directory ilke. applicationConfiguration özelliğini güncelleştirin. |
| Microsoft. Directory/policies/applicationConfiguration/policyAppliedTo/Read | Azure Active Directory içindeki policies. applicationConfiguration özelliğini okuyun. |
| Microsoft. Directory/Servicesorumlularını/appRoleAssignedTo/Update | Azure Active Directory Servicesorumlularını. appRoleAssignedTo özelliğini güncelleştirin. |
| Microsoft. Directory/Servicesorumlularını/Approtaatamalar/güncelleştirme | Azure Active Directory Servicesorumlularını. Approtaatamalar özelliğini güncelleştirin. |
| Microsoft. Directory/Servicesorumlularını/hedef kitle/güncelleştirme | Azure Active Directory Servicesorumlularını. Audience özelliğini güncelleştirin. |
| Microsoft. Directory/Servicesorumlularını/kimlik doğrulaması/güncelleştirme | Azure Active Directory Servicesorumlularını. Authentication özelliğini güncelleştirin. |
| Microsoft. Directory/Servicesorumlularını/temel/güncelleştirme | Azure Active Directory içindeki Servicesorumlularını temel özelliklerini güncelleştirin. |
| Microsoft. Directory/Servicesorumlularını/oluşturma | Azure Active Directory 'de Servicesorumlularını oluşturun. |
| Microsoft. Directory/Servicesorumlularını/kimlik bilgileri/güncelleştirme | Azure Active Directory Servicesorumlularını. Credentials özelliğini güncelleştirin. |
| Microsoft. Directory/Servicesorumlularını/silme | Azure Active Directory Servicesorumlularını silin. |
| Microsoft. Directory/Servicesorumlularını/Owners/Update | Azure Active Directory Servicesorumlularını. Owners özelliğini güncelleştirin. |
| Microsoft. Directory/Servicesorumlularını/izinleri/güncelleştirme | Azure Active Directory Servicesorumlularını. Permissions özelliğini güncelleştirin. |
| Microsoft. Directory/Servicesorumlularını/ilkeleri/güncelleştirme | Azure Active Directory Servicesorumlularını. Policies özelliğini güncelleştirin. |
| Microsoft. Directory/Signınreports/allProperties/Read | Azure Active Directory içindeki Signınreports 'ta tüm özellikleri (ayrıcalıklı özellikler dahil) okuyun. |
| Microsoft. Azure. serviceHealth/Allentitıes/allTasks | Azure hizmet durumunu okuyun ve yapılandırın. |
| Microsoft. Azure. Supportbilet/allEntities/allTasks | Azure destek biletleri oluşturun ve yönetin. |
| Microsoft. office365. serviceHealth/allEntities/allTasks | Office 365 hizmet durumunu okuyun ve yapılandırın. |
| Microsoft. office365. Supportbilet/allEntities/allTasks | Office 365 destek biletleri oluşturun ve yönetin. |

### <a name="cloud-device-administrator-permissions"></a>Bulut aygıtı yönetici izinleri

Azure AD 'de cihazları yönetmek için tam erişim.

| **Eylemler** | **Açıklama** |
| --- | --- |
| Microsoft. Directory/auditLogs/allProperties/Read | Azure Active Directory içindeki auditLogs 'ta tüm özellikleri (ayrıcalıklı özellikler dahil) okuyun. |
| Microsoft. Directory/Devices/bitLockerRecoveryKeys/Read | Azure Active Directory içindeki Devices. bitLockerRecoveryKeys özelliğini okuyun. |
| Microsoft. Directory/Devices/Delete | Azure Active Directory cihazları silin. |
| Microsoft. Directory/cihazlar/devre dışı bırak | Azure Active Directory cihazları devre dışı bırakın. |
| Microsoft. Directory/cihazlar/etkinleştir | Azure Active Directory cihazları etkinleştirin. |
| Microsoft. Directory/Signınreports/allProperties/Read | Azure Active Directory içindeki Signınreports 'ta tüm özellikleri (ayrıcalıklı özellikler dahil) okuyun. |
| Microsoft. Azure. serviceHealth/Allentitıes/allTasks | Azure hizmet durumunu okuyun ve yapılandırın. |
| Microsoft. office365. serviceHealth/allEntities/allTasks | Office 365 hizmet durumunu okuyun ve yapılandırın. |

### <a name="company-administrator-permissions"></a>Şirket Yöneticisi izinleri

, Azure AD kimliklerini kullanan Azure AD ve Microsoft hizmetlerinin tüm yönlerini yönetebilir.

> [!NOTE]
> Bu rol Azure Active Directory dışında ek izinlere sahiptir. Daha fazla bilgi için yukarıdaki rol açıklaması konusuna bakın.
>
>

| **Eylemler** | **Açıklama** |
| --- | --- |
| Microsoft. AAD. cloudAppSecurity/allEntities/allTasks | Tüm kaynakları oluşturup silin ve Microsoft. AAD. cloudAppSecurity içindeki standart özellikleri okuyun ve güncelleştirin. |
| Microsoft. Directory/Yönetimtiveunits/allProperties/allTasks | Yönettiveunits oluşturun ve silin ve Azure Active Directory tüm özellikleri okuyun ve güncelleştirin. |
| Microsoft. Directory/uygulamalar/allProperties/allTasks | Uygulamalar oluşturun ve silin ve Azure Active Directory tüm özellikleri okuyun ve güncelleştirin. |
| Microsoft. Directory/Approtaatamalar/allProperties/allTasks | Approtaatamalar oluşturun ve silin ve Azure Active Directory tüm özellikleri okuyun ve güncelleştirin. |
| Microsoft. Directory/auditLogs/allProperties/Read | Azure Active Directory içindeki auditLogs 'ta tüm özellikleri (ayrıcalıklı özellikler dahil) okuyun. |
| Microsoft. Directory/Contacts/allProperties/allTasks | Kişiler oluşturup silin ve Azure Active Directory tüm özellikleri okuyun ve güncelleştirin. |
| Microsoft. Directory/sözleşmeleri/allProperties/allTasks | Sözleşmeleri oluşturup silin ve Azure Active Directory tüm özellikleri okuyun ve güncelleştirin. |
| Microsoft. Directory/Devices/allProperties/allTasks | Cihazları oluşturun ve silin ve Azure Active Directory tüm özellikleri okuyun ve güncelleştirin. |
| Microsoft. Directory/directoryRoles/allProperties/allTasks | DirectoryRoles oluşturun ve silin ve Azure Active Directory tüm özellikleri okuyun ve güncelleştirin. |
| Microsoft. Directory/directoryRoleTemplates/allProperties/allTasks | DirectoryRoleTemplates oluşturup silin ve Azure Active Directory tüm özellikleri okuyun ve güncelleştirin. |
| Microsoft. Directory/Domains/allProperties/allTasks | Etki alanlarını oluşturun ve silin ve Azure Active Directory tüm özellikleri okuyun ve güncelleştirin. |
| Microsoft. Directory/Groups/allProperties/allTasks | Grupları oluşturun ve silin ve Azure Active Directory tüm özellikleri okuyun ve güncelleştirin. |
| Microsoft. Directory/groupSettings/allProperties/allTasks | GroupSettings oluşturun ve silin ve Azure Active Directory tüm özellikleri okuyun ve güncelleştirin. |
| Microsoft. Directory/groupSettingTemplates/allProperties/allTasks | GroupSettingTemplates oluşturup silin ve Azure Active Directory tüm özellikleri okuyun ve güncelleştirin. |
| Microsoft. Directory/Logınantmarkalaması/allProperties/allTasks | Logınantmarkalaması oluşturun ve silin ve Azure Active Directory tüm özellikleri okuyun ve güncelleştirin. |
| Microsoft. Directory/oAuth2PermissionGrants/allProperties/allTasks | OAuth2PermissionGrants oluşturun ve silin ve Azure Active Directory tüm özellikleri okuyun ve güncelleştirin. |
| Microsoft. Directory/kuruluş/allProperties/allTasks | Kuruluş oluşturup silin ve Azure Active Directory tüm özellikleri okuyun ve güncelleştirin. |
| Microsoft. Directory/policies/allProperties/allTasks | İlkeleri oluşturun ve silin ve Azure Active Directory tüm özellikleri okuyun ve güncelleştirin. |
| Microsoft. Directory/Roleatamaları/allProperties/allTasks | Roleatamalar oluşturun ve silin ve Azure Active Directory tüm özellikleri okuyun ve güncelleştirin. |
| Microsoft. Directory/roleDefinitions/allProperties/allTasks | RoleDefinitions oluşturun ve silin ve Azure Active Directory tüm özellikleri okuyun ve güncelleştirin. |
| Microsoft. Directory/Scopedrolemembersevk/allProperties/allTasks | Scopedrolemembersevk eden oluşturun ve silin ve Azure Active Directory tüm özellikleri okuyun ve güncelleştirin. |
| Microsoft. Directory/serviceAction/activateService | Azure Active Directory 'de Activateservice hizmeti eylemini gerçekleştirebilir |
| Microsoft. Directory/serviceAction/disableDirectoryFeature | Azure Active Directory 'da Disabledirectoryfeature hizmet eylemini gerçekleştirebilir |
| Microsoft. Directory/serviceAction/enableDirectoryFeature | Azure Active Directory 'da Enabledirectoryfeature hizmeti eylemini gerçekleştirebilir |
| Microsoft. Directory/serviceAction/getAvailableExtentionProperties | Azure Active Directory içinde Getavailableextentionproperties hizmeti eylemini gerçekleştirebilir |
| Microsoft. Directory/Servicesorumlularını/allProperties/allTasks | Servicesorumlularını oluşturup silin ve Azure Active Directory tüm özellikleri okuyun ve güncelleştirin. |
| Microsoft. Directory/Signınreports/allProperties/Read | Azure Active Directory içindeki Signınreports 'ta tüm özellikleri (ayrıcalıklı özellikler dahil) okuyun. |
| Microsoft. Directory/subscribedSkus/allProperties/allTasks | SubscribedSkus oluşturun ve silin ve Azure Active Directory tüm özellikleri okuyun ve güncelleştirin. |
| Microsoft. Directory/Users/allProperties/allTasks | Kullanıcı oluşturun ve silin ve Azure Active Directory tüm özellikleri okuyun ve güncelleştirin. |
| Microsoft. directorySync/Allentitıes/allTasks | Azure AD Connect tüm eylemleri gerçekleştirin. |
| Microsoft. AAD. ıdentityprotection/allEntities/allTasks | Tüm kaynakları oluşturup silin ve Microsoft. AAD. ıdentityprotection 'da standart özellikleri okuyun ve güncelleştirin. |
| Microsoft. AAD. Privilegedıdentitymanagement/allEntities/okuma | Microsoft. AAD. Privilegedıdentitymanagement içindeki tüm kaynakları okuyun. |
| Microsoft. Azure. advancedThreatProtection/Allentitıes/Read | Microsoft. Azure. advancedThreatProtection içindeki tüm kaynakları okuyun. |
| Microsoft. Azure. ınformationprotection/allEntities/allTasks | Azure Information Protection tüm yönlerini yönetin. |
| Microsoft. Azure. serviceHealth/Allentitıes/allTasks | Azure hizmet durumunu okuyun ve yapılandırın. |
| Microsoft. Azure. Supportbilet/allEntities/allTasks | Azure destek biletleri oluşturun ve yönetin. |
| Microsoft. Commerce. faturalandırma/allEntities/allTasks | Office 365 faturalandırma 'nin tüm yönlerini yönetin. |
| Microsoft. Intune/allEntities/allTasks | Intune 'un tüm yönlerini yönetin. |
| Microsoft. office365. Karmaşıssuancemanager/allEntities/allTasks | Office 365 uyumluluk Yöneticisi 'nin tüm yönlerini yönetme |
| Microsoft. office365. desktopAnalytics/allEntities/allTasks | Masaüstü analizinin tüm yönlerini yönetin. |
| Microsoft. office365. Exchange/allEntities/allTasks | Exchange Online 'ın tüm yönlerini yönetin. |
| Microsoft. office365. kasa/allEntities/allTasks | Office 365 Müşteri Kasası tüm yönlerini yönetin |
| Microsoft. office365. messageCenter/messages/okundu | Microsoft. office365. messageCenter içindeki iletileri okuyun. |
| Microsoft. office365. messageCenter/securityMessages/Read | Microsoft. office365. messageCenter içindeki securityMessages 'i okuyun. |
| Microsoft. office365. protectionCenter/allEntities/allTasks | Office 365 Koruma Merkezi 'nin tüm yönlerini yönetin. |
| Microsoft. office365. Securityzorluk Ancecenter/allEntities/allTasks | Tüm kaynakları oluşturup silin ve Microsoft. office365. Securitykarmaşıkancecenter içindeki standart özellikleri okuyun ve güncelleştirin. |
| Microsoft. office365. serviceHealth/allEntities/allTasks | Office 365 hizmet durumunu okuyun ve yapılandırın. |
| Microsoft. office365. SharePoint/allEntities/allTasks | Tüm kaynakları oluşturup silin ve Microsoft. office365. SharePoint içindeki standart özellikleri okuyun ve güncelleştirin. |
| Microsoft. office365. Sktypeınfo/allEntities/allTasks | Skype Kurumsal Çevrimiçi 'nin tüm yönlerini yönetin. |
| Microsoft. office365. Supportbilet/allEntities/allTasks | Office 365 destek biletleri oluşturun ve yönetin. |
| Microsoft. office365. usageReports/allEntities/okuma | Office 365 kullanım raporlarını okuyun. |
| Microsoft. office365. webPortal/allEntities/temel/okuma | Microsoft. office365. webPortal 'daki tüm kaynaklarda temel özellikleri okuyun. |
| Microsoft. powerApps. dynamics365/allEntities/allTasks | Dynamics 365 'in tüm yönlerini yönetin. |
| Microsoft. powerApps. PowerBI/allEntities/allTasks | Power BI tüm yönlerini yönetin. |
| Microsoft. Windows. Savunderadvancedthreatprotection/Allentitıes/Read | Microsoft. Windows. savunma Deradvancedthreatprotection içindeki tüm kaynakları okuyun. |

### <a name="compliance-administrator-permissions"></a>Uyumluluk Yöneticisi izinleri

, Azure AD ve Office 365 ' de uyumluluk yapılandırma ve raporlarını okuyabilir ve yönetebilir.

> [!NOTE]
> Bu rol Azure Active Directory dışında ek izinlere sahiptir. Daha fazla bilgi için yukarıdaki rol açıklaması konusuna bakın.
>
>

| **Eylemler** | **Açıklama** |
| --- | --- |
| Microsoft. Azure. serviceHealth/Allentitıes/allTasks | Azure hizmet durumunu okuyun ve yapılandırın. |
| Microsoft. Azure. Supportbilet/allEntities/allTasks | Azure destek biletleri oluşturun ve yönetin. |
| Microsoft. office365. webPortal/allEntities/temel/okuma | Microsoft. office365. webPortal 'daki tüm kaynaklarda temel özellikleri okuyun. |
| Microsoft. office365. Karmaşıssuancemanager/allEntities/allTasks | Office 365 uyumluluk Yöneticisi 'nin tüm yönlerini yönetme |
| Microsoft. office365. Exchange/allEntities/allTasks | Exchange Online 'ın tüm yönlerini yönetin. |
| Microsoft. office365. serviceHealth/allEntities/allTasks | Office 365 hizmet durumunu okuyun ve yapılandırın. |
| Microsoft. office365. SharePoint/allEntities/allTasks | Tüm kaynakları oluşturup silin ve Microsoft. office365. SharePoint içindeki standart özellikleri okuyun ve güncelleştirin. |
| Microsoft. office365. Sktypeınfo/allEntities/allTasks | Skype Kurumsal Çevrimiçi 'nin tüm yönlerini yönetin. |
| Microsoft. office365. Supportbilet/allEntities/allTasks | Office 365 destek biletleri oluşturun ve yönetin. |

### <a name="compliance-data-administrator-permissions"></a>Uyumluluk verileri Yöneticisi izinleri

Uyumluluk içeriğini oluşturur ve yönetir.

> [!NOTE]
> Bu rol Azure Active Directory dışında ek izinlere sahiptir. Daha fazla bilgi için yukarıdaki rol açıklaması konusuna bakın.
>
>

| **Eylemler** | **Açıklama** |
| --- | --- |
| Microsoft. AAD. cloudAppSecurity/allEntities/allTasks | Microsoft Cloud App Security okuyun ve yapılandırın. |
| Microsoft. Azure. ınformationprotection/allEntities/allTasks | Azure Information Protection tüm yönlerini yönetin. |
| Microsoft. Azure. serviceHealth/Allentitıes/allTasks | Azure hizmet durumunu okuyun ve yapılandırın. |
| Microsoft. Azure. Supportbilet/allEntities/allTasks | Azure destek biletleri oluşturun ve yönetin. |
| Microsoft. office365. webPortal/allEntities/temel/okuma | Microsoft. office365. webPortal 'daki tüm kaynaklarda temel özellikleri okuyun. |
| Microsoft. office365. Karmaşıssuancemanager/allEntities/allTasks | Office 365 uyumluluk Yöneticisi 'nin tüm yönlerini yönetme |
| Microsoft. office365. Exchange/allEntities/allTasks | Exchange Online 'ın tüm yönlerini yönetin. |
| Microsoft. office365. serviceHealth/allEntities/allTasks | Office 365 hizmet durumunu okuyun ve yapılandırın. |
| Microsoft. office365. SharePoint/allEntities/allTasks | Tüm kaynakları oluşturup silin ve Microsoft. office365. SharePoint içindeki standart özellikleri okuyun ve güncelleştirin. |
| Microsoft. office365. Sktypeınfo/allEntities/allTasks | Skype Kurumsal Çevrimiçi 'nin tüm yönlerini yönetin. |
| Microsoft. office365. Supportbilet/allEntities/allTasks | Office 365 destek biletleri oluşturun ve yönetin. |

### <a name="conditional-access-administrator-permissions"></a>Koşullu Erişim Yöneticisi izinleri

, Koşullu erişim yeteneklerini yönetebilir.

| **Eylemler** | **Açıklama** |
| --- | --- |
| Microsoft. Directory/policies/conditionalAccess/temel/okuma | Azure Active Directory içindeki policies. conditionalAccess özelliğini okuyun. |
| Microsoft. Directory/policies/conditionalAccess/temel/güncelleştirme | Azure Active Directory policies. conditionalAccess özelliğini güncelleştirin. |
| Microsoft. Directory/policies/Conditionalaişim/oluşturma | Azure Active Directory ilke oluşturun. |
| Microsoft. Directory/policies/conditionalAccess/Delete | Azure Active Directory ilkeleri silin. |
| Microsoft. Directory/policies/conditionalAccess/Owners/Read | Azure Active Directory içindeki policies. conditionalAccess özelliğini okuyun. |
| Microsoft. Directory/policies/conditionalAccess/Owners/Update | Azure Active Directory policies. conditionalAccess özelliğini güncelleştirin. |
| Microsoft. Directory/policies/conditionalAccess/policiesAppliedTo/Read | Azure Active Directory içindeki policies. conditionalAccess özelliğini okuyun. |
| Microsoft. Directory/policies/conditionalAccess/tenantDefault/Update | Azure Active Directory policies. conditionalAccess özelliğini güncelleştirin. |

### <a name="crm-service-administrator-permissions"></a>CRM hizmeti yönetici izinleri

, Dynamics 365 ürününün tüm yönlerini yönetebilir.

> [!NOTE]
> Bu rol Azure Active Directory dışında ek izinlere sahiptir. Daha fazla bilgi için yukarıdaki rol açıklaması konusuna bakın.
>
>

| **Eylemler** | **Açıklama** |
| --- | --- |
| Microsoft. Azure. serviceHealth/Allentitıes/allTasks | Azure hizmet durumunu okuyun ve yapılandırın. |
| Microsoft. Azure. Supportbilet/allEntities/allTasks | Azure destek biletleri oluşturun ve yönetin. |
| Microsoft. powerApps. dynamics365/allEntities/allTasks | Dynamics 365 'in tüm yönlerini yönetin. |
| Microsoft. office365. webPortal/allEntities/temel/okuma | Microsoft. office365. webPortal 'daki tüm kaynaklarda temel özellikleri okuyun. |
| Microsoft. office365. serviceHealth/allEntities/allTasks | Office 365 hizmet durumunu okuyun ve yapılandırın. |
| Microsoft. office365. Supportbilet/allEntities/allTasks | Office 365 destek biletleri oluşturun ve yönetin. |

### <a name="customer-lockbox-access-approver-permissions"></a>Müşteri Kasası erişimi onaylayan izinleri

, Müşteri kurumsal verilerine erişmek için Microsoft destek isteklerini onaylayabilir.

> [!NOTE]
> Bu rol Azure Active Directory dışında ek izinlere sahiptir. Daha fazla bilgi için yukarıdaki rol açıklaması konusuna bakın.
>
>

| **Eylemler** | **Açıklama** |
| --- | --- |
| Microsoft. office365. webPortal/allEntities/temel/okuma | Microsoft. office365. webPortal 'daki tüm kaynaklarda temel özellikleri okuyun. |
| Microsoft. office365. kasa/allEntities/allTasks | Office 365 Müşteri Kasası tüm yönlerini yönetin |

### <a name="desktop-analytics-administrator-permissions"></a>Masaüstü Analizi yönetici izinleri

, Masaüstü Analizi ve Office özelleştirmesi & Ilkesi hizmetlerini yönetebilir. Bu, masaüstü analizi için varlık envanterini görüntüleme, dağıtım planları oluşturma, dağıtım ve sistem durumunu görüntüleme imkanını içerir. Office özelleştirmesi & Ilke hizmetinde, bu rol kullanıcıların Office ilkelerini yönetmesine olanak sağlar.

> [!NOTE]
> Bu rol Azure Active Directory dışında ek izinlere sahiptir. Daha fazla bilgi için yukarıdaki rol açıklaması konusuna bakın.
>
>

| **Eylemler** | **Açıklama** |
| --- | --- |
| Microsoft. Azure. serviceHealth/Allentitıes/allTasks | Azure hizmet durumunu okuyun ve yapılandırın. |
| Microsoft. Azure. Supportbilet/allEntities/allTasks | Azure destek biletleri oluşturun ve yönetin. |
| Microsoft. office365. webPortal/allEntities/temel/okuma | Microsoft. office365. webPortal 'daki tüm kaynaklarda temel özellikleri okuyun. |
| Microsoft. office365. desktopAnalytics/allEntities/allTasks | Masaüstü analizinin tüm yönlerini yönetin. |
| Microsoft. office365. serviceHealth/allEntities/allTasks | Office 365 hizmet durumunu okuyun ve yapılandırın. |
| Microsoft. office365. Supportbilet/allEntities/allTasks | Office 365 destek biletleri oluşturun ve yönetin. |

### <a name="device-administrators-permissions"></a>Cihaz yöneticileri izinleri

Bu role atanan kullanıcılar, Azure AD 'ye katılmış cihazlarda yerel Yöneticiler grubuna eklenir.

| **Eylemler** | **Açıklama** |
| --- | --- |
| Microsoft. Directory/groupSettings/Basic/Read | Azure Active Directory groupSettings üzerindeki temel özellikleri okuyun. |
| Microsoft. Directory/groupSettingTemplates/Basic/Read | Azure Active Directory groupSettingTemplates üzerindeki temel özellikleri okuyun. |

### <a name="directory-readers-permissions"></a>Dizin okuyucular izinleri
Temel dizin bilgilerini okuyabilir. Uygulamalara erişim vermek için, kullanıcılara yönelik değildir.

| **Eylemler** | **Açıklama** |
| --- | --- |
| Microsoft. Directory/Yönetimtiveunits/Basic/Read | Azure Active Directory ' de Yönetimtiveunits üzerindeki temel özellikleri okuyun. |
| Microsoft. Directory/Yönetimtiveunits/Üyeler/Read | Azure Active Directory içindeki Yönetimtiveunits. Members özelliğini okuyun. |
| Microsoft. Directory/uygulamalar/temel/okuma | Azure Active Directory içindeki uygulamalarda temel özellikleri okuyun. |
| Microsoft. Directory/uygulamalar/sahipler/okuma | Azure Active Directory içindeki Applications. Owners özelliğini okuyun. |
| Microsoft. Directory/uygulamalar/ilkeler/okuma | Azure Active Directory içindeki Applications. Policies özelliğini okuyun. |
| Microsoft. Directory/Contacts/Basic/Read | Azure Active Directory kişilerdeki temel özellikleri okuyun. |
| Microsoft. Directory/kiþiler/memberOf/Read | Azure Active Directory içinde Contacts. memberOf özelliğini okuyun. |
| Microsoft. Directory/sözleşmeleri/temel/okuma | Azure Active Directory 'daki sözleşmelerdeki temel özellikleri okuyun. |
| Microsoft. Directory/Devices/Basic/Read | Azure Active Directory cihazlarda temel özellikleri okuyun. |
| Microsoft. Directory/Devices/memberOf/Read | Azure Active Directory içindeki Devices. memberOf özelliğini okuyun. |
| Microsoft. Directory/Devices/kayıt bilgileri/okuma | Azure Active Directory içindeki Devices. Kaydedeteredowners özelliğini okuyun. |
| Microsoft. Directory/Devices/registeredUsers/Read | Azure Active Directory içindeki Devices. registeredUsers özelliğini okuyun. |
| Microsoft. Directory/directoryRoles/Basic/Read | Azure Active Directory 'de directoryRoles temel özelliklerini okuyun. |
| Microsoft. Directory/directoryRoles/eli, Lemembers/Read | Azure Active Directory directoryRoles. Eli, Lemembers özelliğini okuyun. |
| Microsoft. Directory/directoryRoles/Members/Read | Azure Active Directory içindeki directoryRoles. Members özelliğini okuyun. |
| Microsoft. Directory/Domains/Basic/Read | Azure Active Directory etki alanlarında temel özellikleri okuyun. |
| Microsoft. Directory/Groups/Approtaatamalar/Read | Azure Active Directory groups. Approtaatamalar özelliğini okuyun. |
| Microsoft. Directory/Groups/Basic/Read | Azure Active Directory gruplardaki temel özellikleri okuyun. |
| Microsoft. Directory/gruplar/memberOf/Read | Azure Active Directory groups. memberOf özelliğini okuyun. |
| Microsoft. Directory/gruplar/Üyeler/okuma | Azure Active Directory gruplar. Members özelliğini okuyun. |
| Microsoft. Directory/gruplar/Owners/Read | Azure Active Directory içindeki groups. Owners özelliğini okuyun. |
| Microsoft. Directory/Groups/Settings/Read | Azure Active Directory içindeki groups. Settings özelliğini okuyun. |
| Microsoft. Directory/groupSettings/Basic/Read | Azure Active Directory groupSettings üzerindeki temel özellikleri okuyun. |
| Microsoft. Directory/groupSettingTemplates/Basic/Read | Azure Active Directory groupSettingTemplates üzerindeki temel özellikleri okuyun. |
| Microsoft. Directory/oAuth2PermissionGrants/Basic/Read | Azure Active Directory içindeki temel özellikleri okuyun oAuth2PermissionGrants. |
| Microsoft. Directory/kuruluş/temel/okuma | Azure Active Directory içindeki kuruluştaki temel özellikleri okuyun. |
| Microsoft. Directory/Organization/trustedCAsForPasswordlessAuth/okuma | Azure Active Directory içinde Organization. trustedCAsForPasswordlessAuth özelliğini okuyun. |
| Microsoft. Directory/Roleatamalar/temel/okuma | Azure Active Directory içindeki Roleatamalarındaki temel özellikleri okuyun. |
| Microsoft. Directory/roleDefinitions/temel/okuma | Azure Active Directory içindeki roleDefinitions ' daki temel özellikleri okuyun. |
| Microsoft. Directory/Servicesorumlularını/appRoleAssignedTo/Read | Azure Active Directory Servicesorumlularını. appRoleAssignedTo özelliğini okuyun. |
| Microsoft. Directory/Servicesorumlularını/Approtaatamalar/okuma | Azure Active Directory Servicesorumlularını. Approtaatamalar özelliğini okuyun. |
| Microsoft. Directory/Servicesorumlular/Basic/Read | Azure Active Directory içindeki Servicesorumlularını temel özelliklerini okuyun. |
| Microsoft. Directory/Servicesorumlularını/memberOf/Read | Azure Active Directory Servicesorumlularını. memberOf özelliğini okuyun. |
| Microsoft. Directory/Servicesorumlularını/oAuth2PermissionGrants/Basic/Read | Azure Active Directory Servicesorumlularını. oAuth2PermissionGrants özelliğini okuyun. |
| Microsoft. Directory/Servicesorumlularını/ownedObjects/Read | Azure Active Directory Servicesorumlularını. ownedObjects özelliğini okuyun. |
| Microsoft. Directory/Servicesorumlularını/Owners/Read | Azure Active Directory içindeki Servicesorumlularını. Owners özelliğini okuyun. |
| Microsoft. Directory/Servicesorumlularını/ilkeleri/okuma | Azure Active Directory içindeki Servicesorumlularını. Policies özelliğini okuyun. |
| Microsoft. Directory/subscribedSkus/Basic/Read | Azure Active Directory içindeki temel özellikleri okuyun subscribedSkus. |
| Microsoft. Directory/Users/Approtaatamalar/okuma | Azure Active Directory içindeki Users. Approtaatamalar özelliğini okuyun. |
| Microsoft. Directory/Users/Basic/Read | Azure Active Directory kullanıcıların temel özelliklerini okuyun. |
| Microsoft. Directory/Users/directReports/Read | Azure Active Directory Users. directReports özelliğini okuyun. |
| Microsoft. Directory/Users/Manager/okuma | Azure Active Directory içindeki Users. Manager özelliğini okuyun. |
| Microsoft. Directory/kullanıcılar/memberOf/Read | Azure Active Directory içindeki Users. memberOf özelliğini okuyun. |
| Microsoft. Directory/Users/oAuth2PermissionGrants/Basic/Read | Azure Active Directory Users. oAuth2PermissionGrants özelliğini okuyun. |
| Microsoft. Directory/Users/ownedDevices/Read | Azure Active Directory Users. ownedDevices özelliğini okuyun. |
| Microsoft. Directory/Users/ownedObjects/Read | Azure Active Directory Users. ownedObjects özelliğini okuyun. |
| Microsoft. Directory/Users/registeredDevices/Read | Azure Active Directory Users. registeredDevices özelliğini okuyun. |

### <a name="directory-synchronization-accounts-permissions"></a>Dizin eşitleme hesapları izinleri

Yalnızca Azure AD Connect hizmeti tarafından kullanılır.

| **Eylemler** | **Açıklama** |
| --- | --- |
| Microsoft. Directory/kuruluş/dirSync/Update | Azure Active Directory içinde Organization. dirSync özelliğini güncelleştirin. |
| Microsoft. Directory/policies/Create | Azure Active Directory ilke oluşturun. |
| Microsoft. Directory/policies/Delete | Azure Active Directory ilkeleri silin. |
| Microsoft. Directory/policies/Basic/Read | Azure Active Directory ilke üzerindeki temel özellikleri okuyun. |
| Microsoft. Directory/policies/Basic/Update | Azure Active Directory ilkelerde temel özellikleri güncelleştirin. |
| Microsoft. Directory/policies/Owners/Read | Azure Active Directory içindeki policies. Owners özelliğini okuyun. |
| Microsoft. Directory/policies/Owners/Update | Azure Active Directory içindeki policies. Owners özelliğini güncelleştirin. |
| Microsoft. Directory/policies/policiesAppliedTo/Read | Azure Active Directory içindeki policies. policiesAppliedTo özelliğini okuyun. |
| Microsoft. Directory/policies/tenantDefault/Update | Azure Active Directory ilkeleri. tenantDefault özelliğini güncelleştirin. |
| Microsoft. Directory/Servicesorumlularını/appRoleAssignedTo/Read | Azure Active Directory Servicesorumlularını. appRoleAssignedTo özelliğini okuyun. |
| Microsoft. Directory/Servicesorumlularını/appRoleAssignedTo/Update | Azure Active Directory Servicesorumlularını. appRoleAssignedTo özelliğini güncelleştirin. |
| Microsoft. Directory/Servicesorumlularını/Approtaatamalar/okuma | Azure Active Directory Servicesorumlularını. Approtaatamalar özelliğini okuyun. |
| Microsoft. Directory/Servicesorumlularını/Approtaatamalar/güncelleştirme | Azure Active Directory Servicesorumlularını. Approtaatamalar özelliğini güncelleştirin. |
| Microsoft. Directory/Servicesorumlularını/hedef kitle/güncelleştirme | Azure Active Directory Servicesorumlularını. Audience özelliğini güncelleştirin. |
| Microsoft. Directory/Servicesorumlularını/kimlik doğrulaması/güncelleştirme | Azure Active Directory Servicesorumlularını. Authentication özelliğini güncelleştirin. |
| Microsoft. Directory/Servicesorumlular/Basic/Read | Azure Active Directory içindeki Servicesorumlularını temel özelliklerini okuyun. |
| Microsoft. Directory/Servicesorumlularını/temel/güncelleştirme | Azure Active Directory içindeki Servicesorumlularını temel özelliklerini güncelleştirin. |
| Microsoft. Directory/Servicesorumlularını/oluşturma | Azure Active Directory 'de Servicesorumlularını oluşturun. |
| Microsoft. Directory/Servicesorumlularını/kimlik bilgileri/güncelleştirme | Azure Active Directory Servicesorumlularını. Credentials özelliğini güncelleştirin. |
| Microsoft. Directory/Servicesorumlularını/memberOf/Read | Azure Active Directory Servicesorumlularını. memberOf özelliğini okuyun. |
| Microsoft. Directory/Servicesorumlularını/oAuth2PermissionGrants/Basic/Read | Azure Active Directory Servicesorumlularını. oAuth2PermissionGrants özelliğini okuyun. |
| Microsoft. Directory/Servicesorumlularını/Owners/Read | Azure Active Directory içindeki Servicesorumlularını. Owners özelliğini okuyun. |
| Microsoft. Directory/Servicesorumlularını/Owners/Update | Azure Active Directory Servicesorumlularını. Owners özelliğini güncelleştirin. |
| Microsoft. Directory/Servicesorumlularını/ownedObjects/Read | Azure Active Directory Servicesorumlularını. ownedObjects özelliğini okuyun. |
| Microsoft. Directory/Servicesorumlularını/izinleri/güncelleştirme | Azure Active Directory Servicesorumlularını. Permissions özelliğini güncelleştirin. |
| Microsoft. Directory/Servicesorumlularını/ilkeleri/okuma | Azure Active Directory içindeki Servicesorumlularını. Policies özelliğini okuyun. |
| Microsoft. Directory/Servicesorumlularını/ilkeleri/güncelleştirme | Azure Active Directory Servicesorumlularını. Policies özelliğini güncelleştirin. |
| Microsoft. directorySync/Allentitıes/allTasks | Azure AD Connect tüm eylemleri gerçekleştirin. |

### <a name="directory-writers-permissions"></a>Dizin yazıcılar izinleri

Temel dizin bilgilerini okuyabilir & yazabilir. Uygulamalara erişim vermek için, kullanıcılara yönelik değildir.

| **Eylemler** | **Açıklama** |
| --- | --- |
| Microsoft. Directory/gruplar/oluştur | Azure Active Directory grupları oluşturun. |
| Microsoft. Directory/Groups/createAsOwner | Azure Active Directory grupları oluşturun. Oluşturucu ilk sahip olarak eklenir ve oluşturulan nesne, oluşturucunun 250 tarafından oluşturulan nesneler kotasına göre sayılır. |
| Microsoft. Directory/Groups/Approtaatamalar/güncelleştirme | Azure Active Directory groups. Approtaatamalar özelliğini güncelleştirin. |
| Microsoft. Directory/Groups/Basic/Update | Azure Active Directory gruplardaki temel özellikleri güncelleştirin. |
| Microsoft. Directory/gruplar/Üyeler/güncelleştirme | Azure Active Directory groups. Members özelliğini güncelleştirin. |
| Microsoft. Directory/gruplar/Owners/Update | Azure Active Directory groups. Owners özelliğini güncelleştirin. |
| Microsoft. Directory/Groups/Settings/Update | Azure Active Directory groups. Settings özelliğini güncelleştirin. |
| Microsoft. Directory/groupSettings/Basic/Update | Azure Active Directory içindeki groupSettings üzerindeki temel özellikleri güncelleştirin. |
| Microsoft. Directory/groupSettings/Create | Azure Active Directory groupSettings oluşturun. |
| Microsoft. Directory/groupSettings/Delete | Azure Active Directory groupSettings 'i silin. |
| Microsoft. Directory/Users/Approtaatamalar/güncelleştirme | Azure Active Directory Users. Approtaatamalar özelliğini güncelleştirin. |
| Microsoft. Directory/Users/Atamalisansı | Azure Active Directory kullanıcılar üzerinde lisansları yönetin. |
| Microsoft. Directory/Users/Basic/Update | Azure Active Directory kullanıcıların temel özelliklerini güncelleştirin. |
| Microsoft. Directory/Users/ınvalidateallrefreshtokens | Azure Active Directory tüm Kullanıcı yenileme belirteçlerini geçersiz kılar. |
| Microsoft. Directory/Users/Manager/Update | Azure Active Directory içindeki Users. Manager özelliğini güncelleştirin. |
| Microsoft. Directory/Users/userPrincipalName/Update | Azure Active Directory içindeki Users. userPrincipalName özelliğini güncelleştirin. |

### <a name="exchange-service-administrator-permissions"></a>Exchange hizmeti yönetici izinleri

, Exchange ürününün tüm yönlerini yönetebilir.

> [!NOTE]
> Bu rol Azure Active Directory dışında ek izinlere sahiptir. Daha fazla bilgi için yukarıdaki rol açıklaması konusuna bakın.
>
>

| **Eylemler** | **Açıklama** |
| --- | --- |
| Microsoft. Directory/Groups/Unified/Approtaatamalar/Update | Azure Active Directory groups. Unified özelliğini güncelleştirin. |
| Microsoft. Directory/gruplar/Unified/Basic/Update | Office 365 gruplarının temel özelliklerini güncelleştirin. |
| Microsoft. Directory/gruplar/Birleşik/oluşturma | Office 365 grupları oluşturun. |
| Microsoft. Directory/gruplar/Birleşik/Delete | Office 365 gruplarını silin. |
| Microsoft. Directory/gruplar/Birleşik/Üyeler/güncelleştirme | Office 365 gruplarının üyeliğini güncelleştirin. |
| Microsoft. Directory/gruplar/Birleşik/sahipler/güncelleştirme | Office 365 gruplarının sahipliğini güncelleştirme. |
| Microsoft. Azure. serviceHealth/Allentitıes/allTasks | Azure hizmet durumunu okuyun ve yapılandırın. |
| Microsoft. Azure. Supportbilet/allEntities/allTasks | Azure destek biletleri oluşturun ve yönetin. |
| Microsoft. office365. webPortal/allEntities/temel/okuma | Microsoft. office365. webPortal 'daki tüm kaynaklarda temel özellikleri okuyun. |
| Microsoft. office365. Exchange/allEntities/allTasks | Exchange Online 'ın tüm yönlerini yönetin. |
| Microsoft. office365. serviceHealth/allEntities/allTasks | Office 365 hizmet durumunu okuyun ve yapılandırın. |
| Microsoft. office365. Supportbilet/allEntities/allTasks | Office 365 destek biletleri oluşturun ve yönetin. |

### <a name="external-identity-provider-administrator-permissions"></a>Dış kimlik sağlayıcısı yönetici izinleri

Kimlik sağlayıcılarını doğrudan federasyonda kullanılmak üzere yapılandırın.

| **Eylemler** | **Açıklama** |
| --- | --- |
| Microsoft. AAD. B2C/IdentityProviders/allTasks | Azure Active Directory B2C kimlik sağlayıcılarını okuyun ve yapılandırın. |

### <a name="global-reader-permissions"></a>Genel okuyucu izinleri
Genel yöneticinin yapabileceği her şeyi okuyabilir, ancak düzenleyemez. 

> [!NOTE]
> Bu rol Azure Active Directory dışında ek izinlere sahiptir. Daha fazla bilgi için yukarıdaki [rol açıklaması](#global-reader) konusuna bakın.
>
>

| **Eylemler** | **Açıklama** |
| --- | --- |
| Microsoft. Commerce. faturalandırma/allEntities/okuma   | Office 365 faturalandırması 'nın tüm yönlerini okuyun. |
| Microsoft. Directory/Yönetimtiveunits/Basic/Read    | Azure Active Directory ' de Yönetimtiveunits üzerindeki temel özellikleri okuyun. |
| Microsoft. Directory/Yönetimtiveunits/Üyeler/Read  | Azure Active Directory içindeki Yönetimtiveunits. Members özelliğini okuyun. |
| Microsoft. Directory/uygulamalar/temel/okuma   | Azure Active Directory içindeki uygulamalarda temel özellikleri okuyun. |
| Microsoft. Directory/uygulamalar/sahipler/okuma  | Azure Active Directory içindeki Applications. Owners özelliğini okuyun. |
| Microsoft. Directory/uygulamalar/ilkeler/okuma    | Azure Active Directory içindeki Applications. Policies özelliğini okuyun. |
| Microsoft. Directory/Contacts/Basic/Read   | Azure Active Directory kişilerdeki temel özellikleri okuyun. |
| Microsoft. Directory/kiþiler/memberOf/Read    | Azure Active Directory içinde Contacts. memberOf özelliğini okuyun. |
| Microsoft. Directory/sözleşmeleri/temel/okuma  | Azure Active Directory 'daki sözleşmelerdeki temel özellikleri okuyun. |
| Microsoft. Directory/Devices/Basic/Read    | Azure Active Directory cihazlarda temel özellikleri okuyun. |
| Microsoft. Directory/Devices/memberOf/Read | Azure Active Directory içindeki Devices. memberOf özelliğini okuyun. |
| Microsoft. Directory/Devices/kayıt bilgileri/okuma | Azure Active Directory içindeki Devices. Kaydedeteredowners özelliğini okuyun. |
| Microsoft. Directory/Devices/registeredUsers/Read  | Azure Active Directory içindeki Devices. registeredUsers özelliğini okuyun. |
| Microsoft. Directory/directoryRoles/Basic/Read | Azure Active Directory 'de directoryRoles temel özelliklerini okuyun. |
| Microsoft. Directory/directoryRoles/eli, Lemembers/Read   | Azure Active Directory directoryRoles. Eli, Lemembers özelliğini okuyun. |
| Microsoft. Directory/directoryRoles/Members/Read   | Azure Active Directory içindeki directoryRoles. Members özelliğini okuyun. |
| Microsoft. Directory/Domains/Basic/Read    | Azure Active Directory etki alanlarında temel özellikleri okuyun. |
| Microsoft. Directory/Groups/Approtaatamalar/Read    | Azure Active Directory groups. Approtaatamalar özelliğini okuyun. |
| Microsoft. Directory/Groups/Basic/Read | Azure Active Directory gruplardaki temel özellikleri okuyun. |
| Microsoft. Directory/Groups/hiddenMembers/Read | Azure Active Directory gruplar. hiddenMembers özelliğini okuyun. |
| Microsoft. Directory/gruplar/memberOf/Read  | Azure Active Directory groups. memberOf özelliğini okuyun. |
| Microsoft. Directory/gruplar/Üyeler/okuma   | Azure Active Directory gruplar. Members özelliğini okuyun. |
| Microsoft. Directory/gruplar/Owners/Read    | Azure Active Directory içindeki groups. Owners özelliğini okuyun. |
| Microsoft. Directory/Groups/Settings/Read  | Azure Active Directory içindeki groups. Settings özelliğini okuyun. |
| Microsoft. Directory/groupSettings/Basic/Read  | Azure Active Directory groupSettings üzerindeki temel özellikleri okuyun. |
| Microsoft. Directory/groupSettingTemplates/Basic/Read  | Azure Active Directory groupSettingTemplates üzerindeki temel özellikleri okuyun. |
| Microsoft. Directory/oAuth2PermissionGrants/Basic/Read | Azure Active Directory içindeki temel özellikleri okuyun oAuth2PermissionGrants. |
| Microsoft. Directory/kuruluş/temel/okuma   | Azure Active Directory içindeki kuruluştaki temel özellikleri okuyun. |
| Microsoft. Directory/Organization/trustedCAsForPasswordlessAuth/okuma   | Azure Active Directory içinde Organization. trustedCAsForPasswordlessAuth özelliğini okuyun. |
| Microsoft. Directory/policies/standart/Read    | Azure Active Directory içindeki standart ilkeleri okuyun. |
| Microsoft. Directory/Roleatamalar/temel/okuma    | Azure Active Directory içindeki Roleatamalarındaki temel özellikleri okuyun. |
| Microsoft. Directory/roleDefinitions/temel/okuma    | Azure Active Directory içindeki roleDefinitions ' daki temel özellikleri okuyun. |
| Microsoft. Directory/Servicesorumlularını/appRoleAssignedTo/Read  | Azure Active Directory Servicesorumlularını. appRoleAssignedTo özelliğini okuyun. |
| Microsoft. Directory/Servicesorumlularını/Approtaatamalar/okuma | Azure Active Directory Servicesorumlularını. Approtaatamalar özelliğini okuyun. |
| Microsoft. Directory/Servicesorumlular/Basic/Read  | Azure Active Directory içindeki Servicesorumlularını temel özelliklerini okuyun. |
| Microsoft. Directory/Servicesorumlularını/memberOf/Read   | Azure Active Directory Servicesorumlularını. memberOf özelliğini okuyun. |
| Microsoft. Directory/Servicesorumlularını/oAuth2PermissionGrants/Basic/Read   | Azure Active Directory Servicesorumlularını. oAuth2PermissionGrants özelliğini okuyun. |
| Microsoft. Directory/Servicesorumlularını/ownedObjects/Read   | Azure Active Directory Servicesorumlularını. ownedObjects özelliğini okuyun. |
| Microsoft. Directory/Servicesorumlularını/Owners/Read | Azure Active Directory içindeki Servicesorumlularını. Owners özelliğini okuyun. |
| Microsoft. Directory/Servicesorumlularını/ilkeleri/okuma   | Azure Active Directory içindeki Servicesorumlularını. Policies özelliğini okuyun. |
| Microsoft. Directory/Signınreports/allProperties/Read  | Azure Active Directory içindeki Signınreports 'ta tüm özellikleri (ayrıcalıklı özellikler dahil) okuyun. |
| Microsoft. Directory/subscribedSkus/Basic/Read | Azure Active Directory içindeki temel özellikleri okuyun subscribedSkus. |
| Microsoft. Directory/Users/Approtaatamalar/okuma | Azure Active Directory içindeki Users. Approtaatamalar özelliğini okuyun. |
| Microsoft. Directory/Users/Basic/Read  | Azure Active Directory kullanıcıların temel özelliklerini okuyun. |
| Microsoft. Directory/Users/directReports/Read  | Azure Active Directory Users. directReports özelliğini okuyun. |
| Microsoft. Directory/Users/Manager/okuma    | Azure Active Directory içindeki Users. Manager özelliğini okuyun. |
| Microsoft. Directory/kullanıcılar/memberOf/Read   | Azure Active Directory içindeki Users. memberOf özelliğini okuyun. |
| Microsoft. Directory/Users/oAuth2PermissionGrants/Basic/Read   | Azure Active Directory Users. oAuth2PermissionGrants özelliğini okuyun. |
| Microsoft. Directory/Users/ownedDevices/Read   | Azure Active Directory Users. ownedDevices özelliğini okuyun. |
| Microsoft. Directory/Users/ownedObjects/Read   | Azure Active Directory Users. ownedObjects özelliğini okuyun. |
| Microsoft. Directory/Users/registeredDevices/Read  | Azure Active Directory Users. registeredDevices özelliğini okuyun. |
| Microsoft. Directory/Users/strongAuthentication/Read   | MFA kimlik bilgileri gibi güçlü kimlik doğrulama özelliklerini okuyun. |
| Microsoft. office365. Exchange/allEntities/okuma | Exchange Online 'ın tüm yönlerini okuyun. |
| Microsoft. office365. messageCenter/messages/okundu   | Microsoft. office365. messageCenter içindeki iletileri okuyun. |
| Microsoft. office365. messageCenter/securityMessages/Read   | Microsoft. office365. messageCenter içindeki securityMessages 'i okuyun. |
| Microsoft. office365. protectionCenter/allEntities/okuma | Office 365 Koruma Merkezi 'nin tüm yönlerini okuyun. |
| Microsoft. office365. Securityzorluk Ancecenter/allEntities/Read | Microsoft. office365. Securitykarmaşıkancecenter içindeki tüm standart özellikleri okuyun. |
| Microsoft. office365. usageReports/allEntities/okuma | Office 365 kullanım raporlarını okuyun. |
| Microsoft. office365. webPortal/allEntities/standart/okuma   | Microsoft. office365. webPortal 'daki tüm kaynaklarda standart özellikleri okuyun. |

### <a name="group-administrator"></a>Grup Yöneticisi
, Adlandırma ve süre sonu ilkeleri gibi grupların ve Grup ayarlarının tüm yönlerini yönetebilir

| **Eylemler** | **Açıklama** |
| --- | --- |
| Microsoft. Directory/Groups/Basic/Read | Azure Active Directory gruplardaki gruplar üzerinde standart özellikleri okuyun.  |
| Microsoft. Directory/Groups/Basic/Update | Azure Active Directory gruplardaki temel özellikleri güncelleştirin. |
| Microsoft. Directory/gruplar/oluştur | Azure Active Directory grupları oluşturun. |
| Microsoft. Directory/Groups/createAsOwner | Azure Active Directory grupları oluşturun. Oluşturucu ilk sahip olarak eklenir ve oluşturulan nesne, oluşturucunun 250 tarafından oluşturulan nesneler kotasına göre sayılır. |
| Microsoft. Directory/gruplar/Sil | Azure Active Directory grupları silin. |
| Microsoft. Directory/Groups/hiddenMembers/Read | Azure Active Directory gruplar. hiddenMembers özelliğini okuyun. |
| Microsoft. Directory/gruplar/Üyeler/güncelleştirme | Azure Active Directory groups. Members özelliğini güncelleştirin. |
| Microsoft. Directory/gruplar/Owners/Update | Azure Active Directory groups. Owners özelliğini güncelleştirin. |
| Microsoft. Directory/Groups/restore | Azure Active Directory grupları geri yükleyin. |
| Microsoft. Directory/Groups/Settings/Update | Azure Active Directory groups. Settings özelliğini güncelleştirin. |
| Microsoft. Azure. serviceHealth/Allentitıes/allTasks | Azure hizmet durumunu okuyun ve yapılandırın. |
| Microsoft. Azure. Supportbilet/allEntities/allTasks | Azure destek biletleri oluşturun ve yönetin. |
| Microsoft. office365. messageCenter/messages/okundu | Microsoft. office365. messageCenter içindeki iletileri okuyun. |
| Microsoft. office365. serviceHealth/allEntities/allTasks | Office 365 hizmet durumunu okuyun ve yapılandırın. |
| Microsoft. office365. Supportbilet/allEntities/allTasks | Office 365 destek biletleri oluşturun ve yönetin. |
| Microsoft. office365. usageReports/allEntities/okuma | Office 365 kullanım raporlarını okuyun. |

### <a name="guest-inviter-permissions"></a>Konuk Davetleyici izinleri
' Üyeler konukları davet edebilir ' ayarından bağımsız olarak Konuk kullanıcıları davet edebilir.

| **Eylemler** | **Açıklama** |
| --- | --- |
| Microsoft. Directory/Users/Approtaatamalar/okuma | Azure Active Directory içindeki Users. Approtaatamalar özelliğini okuyun. |
| Microsoft. Directory/Users/Basic/Read | Azure Active Directory kullanıcıların temel özelliklerini okuyun. |
| Microsoft. Directory/Users/directReports/Read | Azure Active Directory Users. directReports özelliğini okuyun. |
| Microsoft. Directory/Users/Davetteguest | Azure Active Directory Konuk kullanıcıları davet edin. |
| Microsoft. Directory/Users/Manager/okuma | Azure Active Directory içindeki Users. Manager özelliğini okuyun. |
| Microsoft. Directory/kullanıcılar/memberOf/Read | Azure Active Directory içindeki Users. memberOf özelliğini okuyun. |
| Microsoft. Directory/Users/oAuth2PermissionGrants/Basic/Read | Azure Active Directory Users. oAuth2PermissionGrants özelliğini okuyun. |
| Microsoft. Directory/Users/ownedDevices/Read | Azure Active Directory Users. ownedDevices özelliğini okuyun. |
| Microsoft. Directory/Users/ownedObjects/Read | Azure Active Directory Users. ownedObjects özelliğini okuyun. |
| Microsoft. Directory/Users/registeredDevices/Read | Azure Active Directory Users. registeredDevices özelliğini okuyun. |

### <a name="helpdesk-administrator-permissions"></a>Yardım Masası yönetici izinleri

, Yönetici olmayanlar ve Yardım Masası yöneticileri için parolaları sıfırlayabilir.

| **Eylemler** | **Açıklama** |
| --- | --- |
| Microsoft. Directory/Devices/bitLockerRecoveryKeys/Read | Azure Active Directory içindeki Devices. bitLockerRecoveryKeys özelliğini okuyun. |
| Microsoft. Directory/Users/ınvalidateallrefreshtokens | Azure Active Directory tüm Kullanıcı yenileme belirteçlerini geçersiz kılar. |
| Microsoft. Directory/Users/Password/Update | Azure Active Directory tüm kullanıcılar için parolaları güncelleştirin. Daha ayrıntılı bilgi için çevrimiçi belgelere bakın. |
| Microsoft. Azure. serviceHealth/Allentitıes/allTasks | Azure hizmet durumunu okuyun ve yapılandırın. |
| Microsoft. Azure. Supportbilet/allEntities/allTasks | Azure destek biletleri oluşturun ve yönetin. |
| Microsoft. office365. webPortal/allEntities/temel/okuma | Microsoft. office365. webPortal 'daki tüm kaynaklarda temel özellikleri okuyun. |
| Microsoft. office365. serviceHealth/allEntities/allTasks | Office 365 hizmet durumunu okuyun ve yapılandırın. |
| Microsoft. office365. Supportbilet/allEntities/allTasks | Office 365 destek biletleri oluşturun ve yönetin. |

### <a name="intune-service-administrator-permissions"></a>Intune Hizmet Yöneticisi izinleri

, Intune ürününün tüm yönlerini yönetebilir.

> [!NOTE]
> Bu rol Azure Active Directory dışında ek izinlere sahiptir. Daha fazla bilgi için yukarıdaki rol açıklaması konusuna bakın.
>
>

| **Eylemler** | **Açıklama** |
| --- | --- |
| Microsoft. Directory/Contacts/Basic/Update | Azure Active Directory kişilerdeki temel özellikleri güncelleştirin. |
| Microsoft. Directory/kişiler/oluştur | Azure Active Directory kişiler oluşturun. |
| Microsoft. Directory/Contacts/Delete | Azure Active Directory kişileri silin. |
| Microsoft. Directory/Devices/Basic/Update | Azure Active Directory cihazlarda temel özellikleri güncelleştirin. |
| Microsoft. Directory/Devices/bitLockerRecoveryKeys/Read | Azure Active Directory içindeki Devices. bitLockerRecoveryKeys özelliğini okuyun. |
| Microsoft. Directory/cihazlar/oluşturma | Azure Active Directory cihazlar oluşturun. |
| Microsoft. Directory/Devices/Delete | Azure Active Directory cihazları silin. |
| Microsoft. Directory/Devices/kayıt araçları/güncelleştirme | Azure Active Directory Devices. Kaydedeteredowners özelliğini güncelleştirin. |
| Microsoft. Directory/Devices/registeredUsers/Update | Azure Active Directory Devices. registeredUsers özelliğini güncelleştirin. |
| Microsoft. Directory/Groups/Approtaatamalar/güncelleştirme | Azure Active Directory groups. Approtaatamalar özelliğini güncelleştirin. |
| Microsoft. Directory/Groups/Basic/Update | Azure Active Directory gruplardaki temel özellikleri güncelleştirin. |
| Microsoft. Directory/gruplar/oluştur | Azure Active Directory grupları oluşturun. |
| Microsoft. Directory/Groups/createAsOwner | Azure Active Directory grupları oluşturun. Oluşturucu ilk sahip olarak eklenir ve oluşturulan nesne, oluşturucunun 250 tarafından oluşturulan nesneler kotasına göre sayılır. |
| Microsoft. Directory/gruplar/Sil | Azure Active Directory grupları silin. |
| Microsoft. Directory/Groups/hiddenMembers/Read | Azure Active Directory gruplar. hiddenMembers özelliğini okuyun. |
| Microsoft. Directory/gruplar/Üyeler/güncelleştirme | Azure Active Directory groups. Members özelliğini güncelleştirin. |
| Microsoft. Directory/gruplar/Owners/Update | Azure Active Directory groups. Owners özelliğini güncelleştirin. |
| Microsoft. Directory/Groups/restore | Azure Active Directory grupları geri yükleyin. |
| Microsoft. Directory/Groups/Settings/Update | Azure Active Directory groups. Settings özelliğini güncelleştirin. |
| Microsoft. Directory/Users/Approtaatamalar/güncelleştirme | Azure Active Directory Users. Approtaatamalar özelliğini güncelleştirin. |
| Microsoft. Directory/Users/Basic/Update | Azure Active Directory kullanıcıların temel özelliklerini güncelleştirin. |
| Microsoft. Directory/Users/Manager/Update | Azure Active Directory içindeki Users. Manager özelliğini güncelleştirin. |
| Microsoft. Azure. Supportbilet/allEntities/allTasks | Azure destek biletleri oluşturun ve yönetin. |
| Microsoft. Intune/allEntities/allTasks | Intune 'un tüm yönlerini yönetin. |
| Microsoft. office365. Supportbilet/allEntities/allTasks | Office 365 destek biletleri oluşturun ve yönetin. |
| Microsoft. office365. webPortal/allEntities/temel/okuma | Microsoft. office365. webPortal 'daki tüm kaynaklarda temel özellikleri okuyun. |

### <a name="kaizala-administrator-permissions"></a>Kaizala yönetici izinleri

, Microsoft Kaizala ayarlarını yönetebilir.

> [!NOTE]
> Bu rol Azure Active Directory dışında ek izinlere sahiptir. Daha fazla bilgi için yukarıdaki rol açıklaması konusuna bakın.
>
>

| **Eylemler** | **Açıklama** |
| --- | --- |
| Microsoft. office365. serviceHealth/allEntities/allTasks | Office 365 hizmet durumunu okuyun ve yapılandırın. |
| Microsoft. office365. Supportbilet/allEntities/allTasks | Office 365 destek biletleri oluşturun ve yönetin. |
| Microsoft. office365. webPortal/allEntities/temel/okuma | Office 365 Yönetim merkezini okuyun. |

### <a name="license-administrator-permissions"></a>Lisans Yöneticisi izinleri

, Kullanıcılar ve gruplar üzerinde Ürün lisanslarını yönetebilir.

| **Eylemler** | **Açıklama** |
| --- | --- |
| Microsoft. Directory/Users/Atamalisansı | Azure Active Directory kullanıcılar üzerinde lisansları yönetin. |
| Microsoft. Directory/Users/usageLocation/Update | Azure Active Directory Users. usageLocation özelliğini güncelleştirin. |
| Microsoft. Azure. serviceHealth/Allentitıes/allTasks | Azure hizmet durumunu okuyun ve yapılandırın. |
| Microsoft. office365. webPortal/allEntities/temel/okuma | Microsoft. office365. webPortal 'daki tüm kaynaklarda temel özellikleri okuyun. |
| Microsoft. office365. serviceHealth/allEntities/allTasks | Office 365 hizmet durumunu okuyun ve yapılandırın. |

### <a name="lync-service-administrator-permissions"></a>Lync hizmeti yönetici izinleri

, Skype Kurumsal ürününün tüm yönlerini yönetebilir.

> [!NOTE]
> Bu rol Azure Active Directory dışında ek izinlere sahiptir. Daha fazla bilgi için yukarıdaki rol açıklaması konusuna bakın.
>
>

| **Eylemler** | **Açıklama** |
| --- | --- |
| Microsoft. Azure. serviceHealth/Allentitıes/allTasks | Azure hizmet durumunu okuyun ve yapılandırın. |
| Microsoft. Azure. Supportbilet/allEntities/allTasks | Azure destek biletleri oluşturun ve yönetin. |
| Microsoft. office365. webPortal/allEntities/temel/okuma | Microsoft. office365. webPortal 'daki tüm kaynaklarda temel özellikleri okuyun. |
| Microsoft. office365. serviceHealth/allEntities/allTasks | Office 365 hizmet durumunu okuyun ve yapılandırın. |
| Microsoft. office365. Sktypeınfo/allEntities/allTasks | Skype Kurumsal Çevrimiçi 'nin tüm yönlerini yönetin. |
| Microsoft. office365. Supportbilet/allEntities/allTasks | Office 365 destek biletleri oluşturun ve yönetin. |

### <a name="message-center-privacy-reader-permissions"></a>İleti Merkezi Gizlilik okuyucusu izinleri

Ileti merkezi gönderilerini, veri gizliliği iletilerini, grupları, etki alanlarını ve abonelikleri okuyabilir.

> [!NOTE]
> Bu rol Azure Active Directory dışında ek izinlere sahiptir. Daha fazla bilgi için yukarıdaki rol açıklaması konusuna bakın.
>
>

| **Eylemler** | **Açıklama** |
| --- | --- |
| Microsoft. office365. webPortal/allEntities/temel/okuma | Microsoft. office365. webPortal 'daki tüm kaynaklarda temel özellikleri okuyun. |
| Microsoft. office365. messageCenter/messages/okundu | Microsoft. office365. messageCenter içindeki iletileri okuyun. |
| Microsoft. office365. messageCenter/securityMessages/Read | Microsoft. office365. messageCenter içindeki securityMessages 'i okuyun. |

### <a name="message-center-reader-permissions"></a>İleti Merkezi okuyucu izinleri
, Yalnızca Office 365 Ileti merkezindeki kuruluşlarındaki iletileri ve güncelleştirmeleri okuyabilir. 

> [!NOTE]
> Bu rol Azure Active Directory dışında ek izinlere sahiptir. Daha fazla bilgi için yukarıdaki rol açıklaması konusuna bakın.
>
>

| **Eylemler** | **Açıklama** |
| --- | --- |
| Microsoft. office365. webPortal/allEntities/temel/okuma | Microsoft. office365. webPortal 'daki tüm kaynaklarda temel özellikleri okuyun. |
| Microsoft. office365. messageCenter/messages/okundu | Microsoft. office365. messageCenter içindeki iletileri okuyun. |

### <a name="office-apps-administrator-permissions"></a>Office uygulamaları yönetici izinleri
, İlke ve ayarlar yönetimi dahil olmak üzere Office uygulamalarının bulut hizmetlerini yönetebilir ve son kullanıcının cihazlarına "yenilikler" özellik içeriğini seçme, seçme ve yayımlama yeteneğini yönetme olanağı sağlar.

> [!NOTE]
> Bu rol Azure Active Directory dışında ek izinlere sahiptir. Daha fazla bilgi için yukarıdaki rol açıklaması konusuna bakın.
>
>

| **Eylemler** | **Açıklama** |
| --- | --- |
| Microsoft. Azure. serviceHealth/Allentitıes/allTasks | Azure hizmet durumunu okuyun ve yapılandırın. |
| Microsoft. Azure. Supportbilet/allEntities/allTasks | Azure destek biletleri oluşturun ve yönetin. |
| Microsoft. office365. messageCenter/messages/okundu | Microsoft. office365. messageCenter içindeki iletileri okuyun. |
| Microsoft. office365. serviceHealth/allEntities/allTasks | Office 365 hizmet durumunu okuyun ve yapılandırın. |
| Microsoft. office365. Supportbilet/allEntities/allTasks | Office 365 destek biletleri oluşturun ve yönetin. |
| Microsoft. office365. usageReports/allEntities/okuma | Office 365 kullanım raporlarını okuyun. |
| Microsoft. office365. userCommunication/allEntities/allTasks | Yeni iletilerin görünürlüğünü okuyun ve güncelleştirin. |
| Microsoft. office365. webPortal/allEntities/temel/okuma | Microsoft. office365. webPortal 'daki tüm kaynaklarda temel özellikleri okuyun. |

### <a name="partner-tier1-support-permissions"></a>İş ortağı Katman1 destek izinleri

Kullanmayın-genel kullanıma yönelik değildir.

> [!NOTE]
> Bu rol Azure Active Directory dışında ek izinlere sahiptir. Daha fazla bilgi için yukarıdaki rol açıklaması konusuna bakın.
>
>

| **Eylemler** | **Açıklama** |
| --- | --- |
| Microsoft. Directory/Contacts/Basic/Update | Azure Active Directory kişilerdeki temel özellikleri güncelleştirin. |
| Microsoft. Directory/kişiler/oluştur | Azure Active Directory kişiler oluşturun. |
| Microsoft. Directory/Contacts/Delete | Azure Active Directory kişileri silin. |
| Microsoft. Directory/gruplar/oluştur | Azure Active Directory grupları oluşturun. |
| Microsoft. Directory/Groups/createAsOwner | Azure Active Directory grupları oluşturun. Oluşturucu ilk sahip olarak eklenir ve oluşturulan nesne, oluşturucunun 250 tarafından oluşturulan nesneler kotasına göre sayılır. |
| Microsoft. Directory/gruplar/Üyeler/güncelleştirme | Azure Active Directory groups. Members özelliğini güncelleştirin. |
| Microsoft. Directory/gruplar/Owners/Update | Azure Active Directory groups. Owners özelliğini güncelleştirin. |
| Microsoft. Directory/Users/Approtaatamalar/güncelleştirme | Azure Active Directory Users. Approtaatamalar özelliğini güncelleştirin. |
| Microsoft. Directory/Users/Atamalisansı | Azure Active Directory kullanıcılar üzerinde lisansları yönetin. |
| Microsoft. Directory/Users/Basic/Update | Azure Active Directory kullanıcıların temel özelliklerini güncelleştirin. |
| Microsoft. Directory/kullanıcılar/Sil | Azure Active Directory Kullanıcıları silin. |
| Microsoft. Directory/Users/ınvalidateallrefreshtokens | Azure Active Directory tüm Kullanıcı yenileme belirteçlerini geçersiz kılar. |
| Microsoft. Directory/Users/Manager/Update | Azure Active Directory içindeki Users. Manager özelliğini güncelleştirin. |
| Microsoft. Directory/Users/Password/Update | Azure Active Directory tüm kullanıcılar için parolaları güncelleştirin. Daha ayrıntılı bilgi için çevrimiçi belgelere bakın. |
| Microsoft. Directory/kullanıcılar/geri yükleme | Azure Active Directory silinen kullanıcıları geri yükleyin. |
| Microsoft. Directory/Users/userPrincipalName/Update | Azure Active Directory içindeki Users. userPrincipalName özelliğini güncelleştirin. |
| Microsoft. Azure. serviceHealth/Allentitıes/allTasks | Azure hizmet durumunu okuyun ve yapılandırın. |
| Microsoft. Azure. Supportbilet/allEntities/allTasks | Azure destek biletleri oluşturun ve yönetin. |
| Microsoft. office365. webPortal/allEntities/temel/okuma | Microsoft. office365. webPortal 'daki tüm kaynaklarda temel özellikleri okuyun. |
| Microsoft. office365. serviceHealth/allEntities/allTasks | Office 365 hizmet durumunu okuyun ve yapılandırın. |
| Microsoft. office365. Supportbilet/allEntities/allTasks | Office 365 destek biletleri oluşturun ve yönetin. |

### <a name="partner-tier2-support-permissions"></a>İş ortağı Katman2 destek izinleri

Kullanmayın-genel kullanıma yönelik değildir.

> [!NOTE]
> Bu rol Azure Active Directory dışında ek izinlere sahiptir. Daha fazla bilgi için yukarıdaki rol açıklaması konusuna bakın.
>
>

| **Eylemler** | **Açıklama** |
| --- | --- |
| Microsoft. Directory/Contacts/Basic/Update | Azure Active Directory kişilerdeki temel özellikleri güncelleştirin. |
| Microsoft. Directory/kişiler/oluştur | Azure Active Directory kişiler oluşturun. |
| Microsoft. Directory/Contacts/Delete | Azure Active Directory kişileri silin. |
| Microsoft. Directory/Domains/allTasks | Etki alanlarını oluşturun ve silin ve Azure Active Directory standart özellikleri okuyun ve güncelleştirin. |
| Microsoft. Directory/gruplar/oluştur | Azure Active Directory grupları oluşturun. |
| Microsoft. Directory/gruplar/Sil | Azure Active Directory grupları silin. |
| Microsoft. Directory/gruplar/Üyeler/güncelleştirme | Azure Active Directory groups. Members özelliğini güncelleştirin. |
| Microsoft. Directory/Groups/restore | Azure Active Directory grupları geri yükleyin. |
| Microsoft. Directory/kuruluş/temel/güncelleştirme | Azure Active Directory 'da kuruluştaki temel özellikleri güncelleştirin. |
| Microsoft. Directory/Users/Approtaatamalar/güncelleştirme | Azure Active Directory Users. Approtaatamalar özelliğini güncelleştirin. |
| Microsoft. Directory/Users/Atamalisansı | Azure Active Directory kullanıcılar üzerinde lisansları yönetin. |
| Microsoft. Directory/Users/Basic/Update | Azure Active Directory kullanıcıların temel özelliklerini güncelleştirin. |
| Microsoft. Directory/kullanıcılar/Sil | Azure Active Directory Kullanıcıları silin. |
| Microsoft. Directory/Users/ınvalidateallrefreshtokens | Azure Active Directory tüm Kullanıcı yenileme belirteçlerini geçersiz kılar. |
| Microsoft. Directory/Users/Manager/Update | Azure Active Directory içindeki Users. Manager özelliğini güncelleştirin. |
| Microsoft. Directory/Users/Password/Update | Azure Active Directory tüm kullanıcılar için parolaları güncelleştirin. Daha ayrıntılı bilgi için çevrimiçi belgelere bakın. |
| Microsoft. Directory/kullanıcılar/geri yükleme | Azure Active Directory silinen kullanıcıları geri yükleyin. |
| Microsoft. Directory/Users/userPrincipalName/Update | Azure Active Directory içindeki Users. userPrincipalName özelliğini güncelleştirin. |
| Microsoft. Azure. serviceHealth/Allentitıes/allTasks | Azure hizmet durumunu okuyun ve yapılandırın. |
| Microsoft. Azure. Supportbilet/allEntities/allTasks | Azure destek biletleri oluşturun ve yönetin. |
| Microsoft. office365. webPortal/allEntities/temel/okuma | Microsoft. office365. webPortal 'daki tüm kaynaklarda temel özellikleri okuyun. |
| Microsoft. office365. serviceHealth/allEntities/allTasks | Office 365 hizmet durumunu okuyun ve yapılandırın. |
| Microsoft. office365. Supportbilet/allEntities/allTasks | Office 365 destek biletleri oluşturun ve yönetin. |

### <a name="password-administrator-permissions"></a>Parola yönetici izinleri

, Yönetici olmayan ve parola yöneticileri için parolaları sıfırlayabilir.

| **Eylemler** | **Açıklama** |
| --- | --- |
| Microsoft. Directory/Users/Password/Update | Azure Active Directory tüm kullanıcılar için parolaları güncelleştirin. Daha ayrıntılı bilgi için çevrimiçi belgelere bakın. |
| Microsoft. office365. webPortal/allEntities/temel/okuma | Microsoft. office365. webPortal 'daki tüm kaynaklarda temel özellikleri okuyun. |

### <a name="power-bi-service-administrator-permissions"></a>Power BI hizmeti yönetici izinleri

Power BI ürünün tüm yönlerini yönetebilir.

> [!NOTE]
> Bu rol Azure Active Directory dışında ek izinlere sahiptir. Daha fazla bilgi için yukarıdaki rol açıklaması konusuna bakın.
>
>

| **Eylemler** | **Açıklama** |
| --- | --- |
| Microsoft. Azure. serviceHealth/Allentitıes/allTasks | Azure hizmet durumunu okuyun ve yapılandırın. |
| Microsoft. Azure. Supportbilet/allEntities/allTasks | Azure destek biletleri oluşturun ve yönetin. |
| Microsoft. powerApps. PowerBI/allEntities/allTasks | Power BI tüm yönlerini yönetin. |
| Microsoft. office365. webPortal/allEntities/temel/okuma | Microsoft. office365. webPortal 'daki tüm kaynaklarda temel özellikleri okuyun. |
| Microsoft. office365. serviceHealth/allEntities/allTasks | Office 365 hizmet durumunu okuyun ve yapılandırın. |
| Microsoft. office365. Supportbilet/allEntities/allTasks | Office 365 destek biletleri oluşturun ve yönetin. |

### <a name="privileged-authentication-administrator-permissions"></a>Ayrıcalıklı kimlik doğrulama Yöneticisi izinleri

Herhangi bir Kullanıcı (yönetici veya yönetici olmayan) için kimlik doğrulama yöntemi bilgilerini görüntüleme, ayarlama ve sıfırlamaya izin verilir.

| **Eylemler** | **Açıklama** |
| --- | --- |
| Microsoft. Directory/Users/ınvalidateallrefreshtokens | Azure Active Directory tüm Kullanıcı yenileme belirteçlerini geçersiz kılar. |
| Microsoft. Directory/Users/strongAuthentication/Update | MFA kimlik bilgileri gibi güçlü kimlik doğrulama özelliklerini güncelleştirin. |
| Microsoft. Azure. serviceHealth/Allentitıes/allTasks | Azure hizmet durumunu okuyun ve yapılandırın. |
| Microsoft. Azure. Supportbilet/allEntities/allTasks | Azure destek biletleri oluşturun ve yönetin. |
| Microsoft. office365. webPortal/allEntities/temel/okuma | Microsoft. office365. webPortal 'daki tüm kaynaklarda temel özellikleri okuyun. |
| Microsoft. office365. serviceHealth/allEntities/allTasks | Office 365 hizmet durumunu okuyun ve yapılandırın. |
| Microsoft. office365. Supportbilet/allEntities/allTasks | Office 365 destek biletleri oluşturun ve yönetin. |
| Microsoft. Directory/Users/Password/Update | Office 365 kuruluşundaki tüm kullanıcılar için parolaları güncelleştirin. Daha ayrıntılı bilgi için çevrimiçi belgelere bakın. |

### <a name="privileged-role-administrator-permissions"></a>Ayrıcalıklı rol Yöneticisi izinleri

, Azure AD 'de rol atamalarını ve tüm Privileged Identity Management yönlerini yönetebilir.

> [!NOTE]
> Bu rol Azure Active Directory dışında ek izinlere sahiptir. Daha fazla bilgi için yukarıdaki rol açıklaması konusuna bakın.
>
>

| **Eylemler** | **Açıklama** |
| --- | --- |
| Microsoft. AAD. Privilegedıdentitymanagement/allEntities/allTasks | Tüm kaynakları oluşturup silin ve Microsoft. AAD. Privilegedıdentitymanagement içindeki standart özellikleri okuyun ve güncelleştirin. |
| Microsoft. Directory/Servicesorumlularını/appRoleAssignedTo/allTasks | Azure Active Directory Servicesorumlularını. appRoleAssignedTo özelliğini okuyun ve yapılandırın. |
| Microsoft. Directory/Servicesorumlularını/oAuth2PermissionGrants/allTasks | Azure Active Directory Servicesorumlularını. oAuth2PermissionGrants özelliğini okuyun ve yapılandırın. |
| Microsoft. Directory/Yönetimtiveunits/allProperties/allTasks | Yönetim birimleri oluşturma ve yönetme (Üyeler dahil) |
| Microsoft. Directory/Roleatamaları/allProperties/allTasks | Rol atamaları oluşturun ve yönetin. |
| Microsoft. Directory/roleDefinitions/allProperties/allTasks | Rol tanımları oluşturun ve yönetin. |

### <a name="reports-reader-permissions"></a>Rapor okuyucusu izinleri

, Oturum açma ve Denetim raporlarını okuyabilir.

> [!NOTE]
> Bu rol Azure Active Directory dışında ek izinlere sahiptir. Daha fazla bilgi için yukarıdaki rol açıklaması konusuna bakın.
>
>

| **Eylemler** | **Açıklama** |
| --- | --- |
| Microsoft. Directory/auditLogs/allProperties/Read | Azure Active Directory içindeki auditLogs 'ta tüm özellikleri (ayrıcalıklı özellikler dahil) okuyun. |
| Microsoft. Directory/Signınreports/allProperties/Read | Azure Active Directory içindeki Signınreports 'ta tüm özellikleri (ayrıcalıklı özellikler dahil) okuyun. |
| Microsoft. Azure. serviceHealth/Allentitıes/allTasks | Azure hizmet durumunu okuyun ve yapılandırın. |
| Microsoft. office365. usageReports/allEntities/okuma | Office 365 kullanım raporlarını okuyun. |

### <a name="search-administrator-permissions"></a>Yönetici izinlerinde ara

, Microsoft Search ayarlarının tüm yönlerini oluşturabilir ve yönetebilir.

> [!NOTE]
> Bu rol Azure Active Directory dışında ek izinlere sahiptir. Daha fazla bilgi için yukarıdaki rol açıklaması konusuna bakın.
>
>

| **Eylemler** | **Açıklama** |
| --- | --- |
| Microsoft. office365. messageCenter/messages/okundu | Microsoft. office365. messageCenter içindeki iletileri okuyun. |
| Microsoft. office365. Search/Allentitıes/allProperties/allTasks | Tüm kaynakları oluşturup silin ve Microsoft. office365. Search içindeki tüm özellikleri okuyun ve güncelleştirin. |
| Microsoft. office365. serviceHealth/allEntities/allTasks | Office 365 hizmet durumunu okuyun ve yapılandırın. |
| Microsoft. office365. Supportbilet/allEntities/allTasks | Office 365 destek biletleri oluşturun ve yönetin. |
| Microsoft. office365. usageReports/allEntities/okuma | Office 365 kullanım raporlarını okuyun. |
| Microsoft. office365. webPortal/allEntities/temel/okuma | Microsoft. office365. webPortal 'daki tüm kaynaklarda temel özellikleri okuyun. |

### <a name="search-editor-permissions"></a>Arama Düzenleyicisi izinleri

Yer işaretleri, Q ve as, konumlar, FloorPlan gibi düzenleme içeriği oluşturabilir ve yönetebilir.

> [!NOTE]
> Bu rol Azure Active Directory dışında ek izinlere sahiptir. Daha fazla bilgi için yukarıdaki rol açıklaması konusuna bakın.
>
>

| **Eylemler** | **Açıklama** |
| --- | --- |
| Microsoft. office365. messageCenter/messages/okundu | Microsoft. office365. messageCenter içindeki iletileri okuyun. |
| Microsoft. office365. Search/Content/allProperties/allTasks | İçerik oluşturun ve silin ve Microsoft. office365. Search içindeki tüm özellikleri okuyun ve güncelleştirin. |
| Microsoft. office365. usageReports/allEntities/okuma | Office 365 kullanım raporlarını okuyun. |

### <a name="security-administrator-permissions"></a>Güvenlik Yöneticisi izinleri

Azure AD ve Office 365 ' de güvenlik bilgilerini ve raporlarını okuyabilir ve yapılandırmayı yönetebilir.

> [!NOTE]
> Bu rol Azure Active Directory dışında ek izinlere sahiptir. Daha fazla bilgi için yukarıdaki rol açıklaması konusuna bakın.
>
>

| **Eylemler** | **Açıklama** |
| --- | --- |
| Microsoft. Directory/uygulamalar/ilkeler/güncelleştirme | Azure Active Directory içindeki Applications. Policies özelliğini güncelleştirin. |
| Microsoft. Directory/auditLogs/allProperties/Read | Azure Active Directory içindeki auditLogs 'ta tüm özellikleri (ayrıcalıklı özellikler dahil) okuyun. |
| Microsoft. Directory/Devices/bitLockerRecoveryKeys/Read | Azure Active Directory içindeki Devices. bitLockerRecoveryKeys özelliğini okuyun. |
| Microsoft. Directory/policies/Basic/Update | Azure Active Directory ilkelerde temel özellikleri güncelleştirin. |
| Microsoft. Directory/policies/Create | Azure Active Directory ilke oluşturun. |
| Microsoft. Directory/policies/Delete | Azure Active Directory ilkeleri silin. |
| Microsoft. Directory/policies/Owners/Update | Azure Active Directory içindeki policies. Owners özelliğini güncelleştirin. |
| Microsoft. Directory/policies/tenantDefault/Update | Azure Active Directory ilkeleri. tenantDefault özelliğini güncelleştirin. |
| Microsoft. Directory/Servicesorumlularını/ilkeleri/güncelleştirme | Azure Active Directory Servicesorumlularını. Policies özelliğini güncelleştirin. |
| Microsoft. Directory/Signınreports/allProperties/Read | Azure Active Directory içindeki Signınreports 'ta tüm özellikleri (ayrıcalıklı özellikler dahil) okuyun. |
| Microsoft. AAD. ıdentityprotection/Allentitıes/Read | Microsoft. AAD. ıdentityprotection içindeki tüm kaynakları okuyun. |
| Microsoft. AAD. ıdentityprotection/allEntities/Update | Microsoft. AAD. ıdentityprotection içindeki tüm kaynakları güncelleştirin. |
| Microsoft. AAD. Privilegedıdentitymanagement/allEntities/okuma | Microsoft. AAD. Privilegedıdentitymanagement içindeki tüm kaynakları okuyun. |
| Microsoft. Azure. serviceHealth/Allentitıes/allTasks | Azure hizmet durumunu okuyun ve yapılandırın. |
| Microsoft. office365. webPortal/allEntities/temel/okuma | Microsoft. office365. webPortal 'daki tüm kaynaklarda temel özellikleri okuyun. |
| Microsoft. office365. protectionCenter/allEntities/okuma | Office 365 Koruma Merkezi 'nin tüm yönlerini okuyun. |
| Microsoft. office365. protectionCenter/allEntities/Update | Microsoft. office365. protectionCenter 'daki tüm kaynakları güncelleştirin. |
| Microsoft. office365. serviceHealth/allEntities/allTasks | Office 365 hizmet durumunu okuyun ve yapılandırın. |

### <a name="security-operator-permissions"></a>Güvenlik Işleci izinleri

Güvenlik olaylarını oluşturur ve yönetir.

> [!NOTE]
> Bu rol Azure Active Directory dışında ek izinlere sahiptir. Daha fazla bilgi için yukarıdaki rol açıklaması konusuna bakın.
>
>

| **Eylemler** | **Açıklama** |
| --- | --- |
| Microsoft. AAD. cloudAppSecurity/allEntities/allTasks | Microsoft Cloud App Security okuyun ve yapılandırın. |
| Microsoft. AAD. ıdentityprotection/Allentitıes/Read | Microsoft. AAD. ıdentityprotection içindeki tüm kaynakları okuyun. |
| Microsoft. AAD. Privilegedıdentitymanagement/allEntities/okuma | Microsoft. AAD. Privilegedıdentitymanagement içindeki tüm kaynakları okuyun. |
| Microsoft. Azure. advancedThreatProtection/Allentitıes/Read | Azure AD Gelişmiş tehdit korumasını okuyun ve yapılandırın. |
| Microsoft. Intune/allEntities/allTasks | Intune 'un tüm yönlerini yönetin. |
| Microsoft. office365. Securityzorluk Ancecenter/allEntities/allTasks | Güvenlik ve Uyumluluk Merkezi okuyun ve yapılandırın. |
| Microsoft. office365. usageReports/allEntities/okuma | Office 365 kullanım raporlarını okuyun. |
| Microsoft. Windows. Savunderadvancedthreatprotection/Allentitıes/Read | Windows Defender Gelişmiş tehdit koruması 'nı okuyun ve yapılandırın. |

### <a name="security-reader-permissions"></a>Güvenlik okuyucusu izinleri

, Azure AD ve Office 365 ' deki güvenlik bilgilerini ve raporlarını okuyabilir.

> [!NOTE]
> Bu rol Azure Active Directory dışında ek izinlere sahiptir. Daha fazla bilgi için yukarıdaki rol açıklaması konusuna bakın.
>
>

| **Eylemler** | **Açıklama** |
| --- | --- |
| Microsoft. Directory/auditLogs/allProperties/Read | Azure Active Directory içindeki auditLogs 'ta tüm özellikleri (ayrıcalıklı özellikler dahil) okuyun. |
| Microsoft. Directory/Devices/bitLockerRecoveryKeys/Read | Azure Active Directory içindeki Devices. bitLockerRecoveryKeys özelliğini okuyun. |
| Microsoft. Directory/Signınreports/allProperties/Read | Azure Active Directory içindeki Signınreports 'ta tüm özellikleri (ayrıcalıklı özellikler dahil) okuyun. |
| Microsoft. AAD. ıdentityprotection/Allentitıes/Read | Microsoft. AAD. ıdentityprotection içindeki tüm kaynakları okuyun. |
| Microsoft. AAD. Privilegedıdentitymanagement/allEntities/okuma | Microsoft. AAD. Privilegedıdentitymanagement içindeki tüm kaynakları okuyun. |
| Microsoft. Azure. serviceHealth/Allentitıes/allTasks | Azure hizmet durumunu okuyun ve yapılandırın. |
| Microsoft. office365. webPortal/allEntities/temel/okuma | Microsoft. office365. webPortal 'daki tüm kaynaklarda temel özellikleri okuyun. |
| Microsoft. office365. protectionCenter/allEntities/okuma | Office 365 Koruma Merkezi 'nin tüm yönlerini okuyun. |
| Microsoft. office365. serviceHealth/allEntities/allTasks | Office 365 hizmet durumunu okuyun ve yapılandırın. |

### <a name="service-support-administrator-permissions"></a>Hizmet desteği yönetici izinleri

, Hizmet durumu bilgilerini okuyabilir ve destek biletlerini yönetebilir.

> [!NOTE]
> Bu rol Azure Active Directory dışında ek izinlere sahiptir. Daha fazla bilgi için yukarıdaki rol açıklaması konusuna bakın.
>
>

| **Eylemler** | **Açıklama** |
| --- | --- |
| Microsoft. Azure. serviceHealth/Allentitıes/allTasks | Azure hizmet durumunu okuyun ve yapılandırın. |
| Microsoft. Azure. Supportbilet/allEntities/allTasks | Azure destek biletleri oluşturun ve yönetin. |
| Microsoft. office365. webPortal/allEntities/temel/okuma | Microsoft. office365. webPortal 'daki tüm kaynaklarda temel özellikleri okuyun. |
| Microsoft. office365. serviceHealth/allEntities/allTasks | Office 365 hizmet durumunu okuyun ve yapılandırın. |
| Microsoft. office365. Supportbilet/allEntities/allTasks | Office 365 destek biletleri oluşturun ve yönetin. |

### <a name="sharepoint-service-administrator-permissions"></a>SharePoint Hizmet Yöneticisi izinleri

, SharePoint hizmetinin tüm yönlerini yönetebilir.

> [!NOTE]
> Bu rol Azure Active Directory dışında ek izinlere sahiptir. Daha fazla bilgi için yukarıdaki rol açıklaması konusuna bakın.
>
>

| **Eylemler** | **Açıklama** |
| --- | --- |
| Microsoft. Directory/Groups/Unified/Approtaatamalar/Update | Azure Active Directory groups. Unified özelliğini güncelleştirin. |
| Microsoft. Directory/gruplar/Unified/Basic/Update | Office 365 gruplarının temel özelliklerini güncelleştirin. |
| Microsoft. Directory/gruplar/Birleşik/oluşturma | Office 365 grupları oluşturun. |
| Microsoft. Directory/gruplar/Birleşik/Delete | Office 365 gruplarını silin. |
| Microsoft. Directory/gruplar/Birleşik/Üyeler/güncelleştirme | Office 365 gruplarının üyeliğini güncelleştirin. |
| Microsoft. Directory/gruplar/Birleşik/sahipler/güncelleştirme | Office 365 gruplarının sahipliğini güncelleştirme. |
| Microsoft. Azure. serviceHealth/Allentitıes/allTasks | Azure hizmet durumunu okuyun ve yapılandırın. |
| Microsoft. Azure. Supportbilet/allEntities/allTasks | Azure destek biletleri oluşturun ve yönetin. |
| Microsoft. office365. webPortal/allEntities/temel/okuma | Microsoft. office365. webPortal 'daki tüm kaynaklarda temel özellikleri okuyun. |
| Microsoft. office365. serviceHealth/allEntities/allTasks | Office 365 hizmet durumunu okuyun ve yapılandırın. |
| Microsoft. office365. SharePoint/allEntities/allTasks | Tüm kaynakları oluşturup silin ve Microsoft. office365. SharePoint içindeki standart özellikleri okuyun ve güncelleştirin. |
| Microsoft. office365. Supportbilet/allEntities/allTasks | Office 365 destek biletleri oluşturun ve yönetin. |

### <a name="teams-communications-administrator-permissions"></a>Takımlar Iletişimleri yönetici izinleri

, Microsoft ekipleri hizmeti içindeki çağrı ve toplantılar özelliklerini yönetebilir.

> [!NOTE]
> Bu rol Azure Active Directory dışında ek izinlere sahiptir. Daha fazla bilgi için yukarıdaki rol açıklaması konusuna bakın.
>
>

| **Eylemler** | **Açıklama** |
| --- | --- |
| Microsoft. Azure. serviceHealth/Allentitıes/allTasks | Azure hizmet durumunu okuyun ve yapılandırın. |
| Microsoft. Azure. Supportbilet/allEntities/allTasks | Azure destek biletleri oluşturun ve yönetin. |
| Microsoft. office365. webPortal/allEntities/temel/okuma | Microsoft. office365. webPortal 'daki tüm kaynaklarda temel özellikleri okuyun. |
| Microsoft. office365. serviceHealth/allEntities/allTasks | Office 365 hizmet durumunu okuyun ve yapılandırın. |
| Microsoft. office365. Supportbilet/allEntities/allTasks | Office 365 destek biletleri oluşturun ve yönetin. |
| Microsoft. office365. usageReports/allEntities/okuma | Office 365 kullanım raporlarını okuyun. |

### <a name="teams-communications-support-engineer-permissions"></a>Takımlar Iletişimleri destek mühendisi izinleri

, Gelişmiş araçları kullanarak takımlar içindeki iletişim sorunlarını giderebilir.

> [!NOTE]
> Bu rol Azure Active Directory dışında ek izinlere sahiptir. Daha fazla bilgi için yukarıdaki rol açıklaması konusuna bakın.
>
>

| **Eylemler** | **Açıklama** |
| --- | --- |
| Microsoft. Azure. serviceHealth/Allentitıes/allTasks | Azure hizmet durumunu okuyun ve yapılandırın. |
| Microsoft. office365. webPortal/allEntities/temel/okuma | Microsoft. office365. webPortal 'daki tüm kaynaklarda temel özellikleri okuyun. |
| Microsoft. office365. serviceHealth/allEntities/allTasks | Office 365 hizmet durumunu okuyun ve yapılandırın. |

### <a name="teams-communications-support-specialist-permissions"></a>Takımlar Iletişimleri destek uzmanı izinleri

Temel araçları kullanarak takımlar içindeki iletişim sorunlarını giderebilirsiniz.

> [!NOTE]
> Bu rol Azure Active Directory dışında ek izinlere sahiptir. Daha fazla bilgi için yukarıdaki rol açıklaması konusuna bakın.
>
>

| **Eylemler** | **Açıklama** |
| --- | --- |
| Microsoft. Azure. serviceHealth/Allentitıes/allTasks | Azure hizmet durumunu okuyun ve yapılandırın. |
| Microsoft. office365. webPortal/allEntities/temel/okuma | Microsoft. office365. webPortal 'daki tüm kaynaklarda temel özellikleri okuyun. |
| Microsoft. office365. serviceHealth/allEntities/allTasks | Office 365 hizmet durumunu okuyun ve yapılandırın. |

### <a name="teams-service-administrator-permissions"></a>Takımlar hizmeti yönetici izinleri

, Microsoft ekipleri hizmetini yönetebilir.

> [!NOTE]
> Bu rol Azure Active Directory dışında ek izinlere sahiptir. Daha fazla bilgi için yukarıdaki rol açıklaması konusuna bakın.
>
>

| **Eylemler** | **Açıklama** |
| --- | --- |
| Microsoft. Directory/Groups/hiddenMembers/Read | Azure Active Directory gruplar. hiddenMembers özelliğini okuyun. |
| Microsoft. Directory/Groups/Unified/Approtaatamalar/Update | Azure Active Directory groups. Unified özelliğini güncelleştirin. |
| Microsoft. Directory/gruplar/Unified/Basic/Update | Office 365 gruplarının temel özelliklerini güncelleştirin. |
| Microsoft. Directory/gruplar/Birleşik/oluşturma | Office 365 grupları oluşturun. |
| Microsoft. Directory/gruplar/Birleşik/Delete | Office 365 gruplarını silin. |
| Microsoft. Directory/gruplar/Birleşik/Üyeler/güncelleştirme | Office 365 gruplarının üyeliğini güncelleştirin. |
| Microsoft. Directory/gruplar/Birleşik/sahipler/güncelleştirme | Office 365 gruplarının sahipliğini güncelleştirme. |
| Microsoft. Azure. serviceHealth/Allentitıes/allTasks | Azure hizmet durumunu okuyun ve yapılandırın. |
| Microsoft. Azure. Supportbilet/allEntities/allTasks | Azure destek biletleri oluşturun ve yönetin. |
| Microsoft. office365. webPortal/allEntities/temel/okuma | Microsoft. office365. webPortal 'daki tüm kaynaklarda temel özellikleri okuyun. |
| Microsoft. office365. serviceHealth/allEntities/allTasks | Office 365 hizmet durumunu okuyun ve yapılandırın. |
| Microsoft. office365. Supportbilet/allEntities/allTasks | Office 365 destek biletleri oluşturun ve yönetin. |
| Microsoft. office365. usageReports/allEntities/okuma | Office 365 kullanım raporlarını okuyun. |

### <a name="user-administrator-permissions"></a>Kullanıcı Yöneticisi izinleri
, Sınırlı yöneticiler için parola sıfırlama dahil olmak üzere kullanıcıların ve grupların tüm yönlerini yönetebilir.

| **Eylemler** | **Açıklama** |
| --- | --- |
| Microsoft. Directory/Approtaatamalar/oluşturma | Azure Active Directory Approlet atamaları oluşturun. |
| Microsoft. Directory/Approtaatamaları/silme | Azure Active Directory Approtaatamaları silin. |
| Microsoft. Directory/Approtaatamalar/güncelleştirme | Azure Active Directory Approtaatamaları güncelleştirin. |
| Microsoft. Directory/Contacts/Basic/Update | Azure Active Directory kişilerdeki temel özellikleri güncelleştirin. |
| Microsoft. Directory/kişiler/oluştur | Azure Active Directory kişiler oluşturun. |
| Microsoft. Directory/Contacts/Delete | Azure Active Directory kişileri silin. |
| Microsoft. Directory/Groups/Approtaatamalar/güncelleştirme | Azure Active Directory groups. Approtaatamalar özelliğini güncelleştirin. |
| Microsoft. Directory/Groups/Basic/Update | Azure Active Directory gruplardaki temel özellikleri güncelleştirin. |
| Microsoft. Directory/gruplar/oluştur | Azure Active Directory grupları oluşturun. |
| Microsoft. Directory/Groups/createAsOwner | Azure Active Directory grupları oluşturun. Oluşturucu ilk sahip olarak eklenir ve oluşturulan nesne, oluşturucunun 250 tarafından oluşturulan nesneler kotasına göre sayılır. |
| Microsoft. Directory/gruplar/Sil | Azure Active Directory grupları silin. |
| Microsoft. Directory/Groups/hiddenMembers/Read | Azure Active Directory gruplar. hiddenMembers özelliğini okuyun. |
| Microsoft. Directory/gruplar/Üyeler/güncelleştirme | Azure Active Directory groups. Members özelliğini güncelleştirin. |
| Microsoft. Directory/gruplar/Owners/Update | Azure Active Directory groups. Owners özelliğini güncelleştirin. |
| Microsoft. Directory/Groups/restore | Azure Active Directory grupları geri yükleyin. |
| Microsoft. Directory/Groups/Settings/Update | Azure Active Directory groups. Settings özelliğini güncelleştirin. |
| Microsoft. Directory/Users/Approtaatamalar/güncelleştirme | Azure Active Directory Users. Approtaatamalar özelliğini güncelleştirin. |
| Microsoft. Directory/Users/Atamalisansı | Azure Active Directory kullanıcılar üzerinde lisansları yönetin. |
| Microsoft. Directory/Users/Basic/Update | Azure Active Directory kullanıcıların temel özelliklerini güncelleştirin. |
| Microsoft. Directory/kullanıcılar/oluştur | Azure Active Directory kullanıcılar oluşturun. |
| Microsoft. Directory/kullanıcılar/Sil | Azure Active Directory Kullanıcıları silin. |
| Microsoft. Directory/Users/ınvalidateallrefreshtokens | Azure Active Directory tüm Kullanıcı yenileme belirteçlerini geçersiz kılar. |
| Microsoft. Directory/Users/Manager/Update | Azure Active Directory içindeki Users. Manager özelliğini güncelleştirin. |
| Microsoft. Directory/Users/Password/Update | Azure Active Directory tüm kullanıcılar için parolaları güncelleştirin. Daha ayrıntılı bilgi için çevrimiçi belgelere bakın. |
| Microsoft. Directory/kullanıcılar/geri yükleme | Azure Active Directory silinen kullanıcıları geri yükleyin. |
| Microsoft. Directory/Users/userPrincipalName/Update | Azure Active Directory içindeki Users. userPrincipalName özelliğini güncelleştirin. |
| Microsoft. Azure. serviceHealth/Allentitıes/allTasks | Azure hizmet durumunu okuyun ve yapılandırın. |
| Microsoft. Azure. Supportbilet/allEntities/allTasks | Azure destek biletleri oluşturun ve yönetin. |
| Microsoft. office365. webPortal/allEntities/temel/okuma | Microsoft. office365. webPortal 'daki tüm kaynaklarda temel özellikleri okuyun. |
| Microsoft. office365. serviceHealth/allEntities/allTasks | Office 365 hizmet durumunu okuyun ve yapılandırın. |
| Microsoft. office365. Supportbilet/allEntities/allTasks | Office 365 destek biletleri oluşturun ve yönetin. |

## <a name="role-template-ids"></a>Rol şablonu kimlikleri

Rol şablonu kimlikleri genellikle Graph API veya PowerShell kullanıcıları tarafından kullanılır.

Grafik displayName | Görünen ad Azure portal | Directoryroletemplateıd
----------------- | ------------------------- | -------------------------
Uygulama Yöneticisi | Uygulama Yöneticisi | 9B895D92-2CD3-44C7-9D02-A6AC2D5EA5C3
Uygulama geliştiricisi | Uygulama geliştiricisi | CF1C38E5-3621-4004-A7CB-879624DCED7C
Kimlik doğrulama Yöneticisi | Kimlik doğrulama Yöneticisi | c4e39bd9-1100-46d3-8c65-fb160da0071f
Azure DevOps Yöneticisi | Azure DevOps Yöneticisi | e3973bdf-4987-49ae-837a-ba8e231c7286
Azure Information Protection Yöneticisi | Azure Information Protection Yöneticisi | 7495fdc4-34c4-4d15-a289-98788ce399fd
B2C Kullanıcı akış Yöneticisi | B2C Kullanıcı akış Yöneticisi | 6e591065-9bad-43ed-90f3-e9424366d2f0
B2C Kullanıcı Akışı Öznitelik Yöneticisi | B2C Kullanıcı Akışı Öznitelik Yöneticisi | 0f971eea-41eb-4569-a71e-57bb8a3eff1e
B2C IEF Anahtar kümesi Yöneticisi | B2C IEF Anahtar kümesi Yöneticisi | aaf43236-0c0d-4d5f-883a-6955382ac081
B2C IEF İlkesi Yöneticisi | B2C IEF İlkesi Yöneticisi | 3edaf663-341e-4475-9f94-5c398ef6c070
Faturalama yöneticisi | Faturalama yöneticisi | b0f54661-2d74-4c50-afa3-1ec803f12efe
Bulut uygulaması Yöneticisi | Bulut uygulaması Yöneticisi | 158c047a-C907-4556-b7ef-446551a6b5f7
Bulut Cihaz Yöneticisi | Bulut Cihaz Yöneticisi | 7698a772-787B-4ac8-901f-60d6b08afd2
Şirket Yöneticisi | Genel yönetici | 62e90394-69f5-4237-9190-012177145e10
Uyumluluk Yöneticisi | Uyumluluk Yöneticisi | 17315797-102D-40b4-93e0-432062caca18
Uyumluluk verileri Yöneticisi | Uyumluluk verileri Yöneticisi | e6d1a23a-da11-4be4-9570-befc86d067a7
Koşullu Erişim Yöneticisi | Koşullu Erişim Yöneticisi | b1be1c3e-b65d-4f19-8427-f6fa0d97feb9
CRM hizmet Yöneticisi | Dynamics 365 Yöneticisi | 44367163-eba1-44c3-98af-f5787879f96a
Müşteri Kasası erişim onaylayıcısı | Müşteri Kasası erişimi onaylayan | 5c4f9dcd-47dc-4cf7-8c9a-9e4207cbfc91
Masaüstü Analizi Yöneticisi | Masaüstü Analizi Yöneticisi | 38a96431-2bdf-4B4C-8B6E-5d3d8abac1a4
Cihaz yöneticileri | Cihaz yöneticileri | 9f06204d-73c1-4d4c-880a-6edb90606fd8
Cihaz katılımı | Cihaz katılımı | 9c094953-4995-41C8-84c8-3ebb9b32c93f
Cihaz yöneticileri | Cihaz yöneticileri | 2b499bcd-da44-4968-8aec-78e1674fa64d
Cihaz kullanıcıları | Cihaz kullanıcıları | d405c6df-0af8-4e3b-95e4-4d06e542189e
Dizin okuyucuları | Dizin okuyucuları | 88d8e3e3-8f55-4a1e-953a-9b9898b8876b
Dizin eşitleme hesapları | Dizin eşitleme hesapları | d29b2b05-8046-44ba-8758-1e26182fcf32
Dizin yazarları | Dizin yazarları | 9360feb5-f418-4baa-8175-e2a00bac4301
Exchange hizmeti Yöneticisi | Exchange Yöneticisi | 29232cdf-9323-42fd-ade2-1d097af3e4de
Dış kimlik sağlayıcısı Yöneticisi | Dış kimlik sağlayıcısı Yöneticisi | be2f45a1-457d-42af-a067-6ec1fa63bc45
Genel okuyucu | Genel okuyucu | f2ef992c-3afb-46b9-b7cf-a126ee74c451
Grup Yöneticisi | Grup Yöneticisi | fdd7a751-b60b-444a-984c-02652fe8fa1c 
Konuk davetci | Konuk davetci | 95e79109-95c0-4d8e-aee3-d01accf2d47b
Yardım Masası Yöneticisi | Parola Yöneticisi | 729827e3-9c14-49f7-bb1b-9608f156bbb8
Intune Hizmet Yöneticisi | Intune Yöneticisi | 3a2c62db-5318-420d-8d74-23afee5d9d5
Kaizala Yöneticisi | Kaizala Yöneticisi | 74ef975b-6605-40af-a5d2-b9539d836353
Lisans Yöneticisi | Lisans Yöneticisi | 4d6ac14f-3453-41d0-bef9-a3e0c569773a
Lync Hizmet Yöneticisi | Skype Kurumsal Yöneticisi | 75941009-915a-4869-ABE7-691bff18279e
İleti Merkezi Gizlilik okuyucusu | İleti Merkezi Gizlilik okuyucusu | ac16e43d-7b2d-40e0-ac05-243ff356ab5b
İleti Merkezi okuyucusu | İleti Merkezi okuyucusu | 790c1fb9-7f7d-4f88-86a1-ef1f95c05c1b
Office uygulamaları Yöneticisi | Office uygulamaları Yöneticisi | 2b745bdf-0803-4d80-aa65-822c4493dadac
Partner Tier1 Desteği | Partner Katman1 desteği | 4ba39ca4-527c-499a-b93d-d9b492c50246
Partner Tier2 Desteği | Partner Katman2 desteği | e00e864a-17c5-4a4b-9c06-f5b95a8d5bd8
Parola Yöneticisi | Parola Yöneticisi | 966707d0-3269-4727-9be2-8c3a10f19b9d
Power BI Hizmet Yöneticisi | Power BI Yöneticisi | a9ea8996-122f-4c74-9520-8edcd192826c
Ayrıcalıklı kimlik doğrulama Yöneticisi | Ayrıcalıklı kimlik doğrulama Yöneticisi | 7be44c8a-adaf-4e2a-84d6-ab2649e08a13
Ayrıcalıklı rol yöneticisi | Ayrıcalıklı rol yöneticisi | e8611ab8-c189-46e8-94e1-60213ab1f814
Rapor okuyucu | Rapor okuyucu | 4a5d8f65-41da-4de4-8968-e035b65339cf
Yönetici ara | Yönetici ara | 0964bb5e-9bdb-4d7b-ac29-58e794862a40
Arama Düzenleyicisi | Arama Düzenleyicisi | 8835291a-918c-4fd7-a9ce-faa49f0cf7d9
Güvenlik Yöneticisi | Güvenlik yöneticisi | 194ae4cb-B126-40b2-bd5b-6091b380977d
Güvenlik operatörü | Güvenlik operatörü | 5f2222b1-57c3-48ba-8ad5-d4759f1fde6f
Güvenlik okuyucusu | Güvenlik okuyucusu | 5d6b6bb7-de71-4623-B4AF-96380a352509
Hizmet desteği Yöneticisi | Hizmet yöneticisi | f023fd81-a637-4b56-95fd-791ac0226033
SharePoint Hizmet Yöneticisi | SharePoint yöneticisi | f28a1f50-f6e7-4571-818b-6a12f2af6b6c
Takımlar Iletişim Yöneticisi | Takımlar Iletişim Yöneticisi | baf37b3a-610e-45da-9e62-d9d1e5e8914b
Takımlar Iletişimleri Destek Mühendisi | Takımlar Iletişimleri Destek Mühendisi | f70938a0-fc10-4177-9e90-2178f8765737
Takımlar Iletişimleri destek uzmanı | Takımlar Iletişimleri destek uzmanı | fcf91098-03e3-41a9-b5ba-6f0ec8188a12
Takımlar Hizmet Yöneticisi | Takımlar Hizmet Yöneticisi | 69091246-20e8-4a56-aa4d-066075b2a7a8
Kullanıcı | Kullanıcı | a0b1b346-4d3e-4e8b-98f8-753987be4970
Kullanıcı Hesabı Yöneticisi | Kullanıcı Yöneticisi | fe930be7-5e62-47db-91af-98c3a49a38b1
Çalışma alanına cihaz katılımı | Çalışma alanına cihaz katılımı | c34f683f-4d5a-4403-AFD-6615e00e3a7f

## <a name="deprecated-roles"></a>Kullanım dışı roller

Aşağıdaki roller kullanılmamalıdır. Bunlar kullanım dışı bırakılmıştır ve gelecekte Azure AD 'den kaldırılacaktır.

* Geçici Lisans Yöneticisi
* Cihaz katılımı
* Cihaz yöneticileri
* Cihaz kullanıcıları
* E-posta doğrulanan kullanıcı Oluşturucu
* Posta kutusu Yöneticisi
* Çalışma alanına cihaz katılımı

## <a name="next-steps"></a>Sonraki adımlar

* Bir kullanıcıyı bir Azure aboneliğinin Yöneticisi olarak atama hakkında daha fazla bilgi edinmek için bkz [. RBAC kullanarak erişimi yönetme ve Azure Portal](../../role-based-access-control/role-assignments-portal.md)
* Microsoft Azure'da kaynak erişiminin nasıl denetlendiği konusunda daha fazla bilgi için bkz. [Azure'da kaynak erişimini anlama](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* Azure Active Directory ile Azure aboneliğinizin arasındaki ilişki hakkında bilgi için bkz. [Azure aboneliklerinin Azure Active Directory ile ilişkisi](../fundamentals/active-directory-how-subscriptions-associated-directory.md)
