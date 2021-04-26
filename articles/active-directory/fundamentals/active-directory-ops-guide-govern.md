---
title: Azure Active Directory idare işlemleri başvuru kılavuzu
description: Bu işlemler başvuru kılavuzu, idare yönetimi 'ni güvenli hale getirmek için gerçekleştirmeniz gereken denetimleri ve eylemleri açıklar
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 10/31/2019
ms.author: martinco
ms.openlocfilehash: ba3ca140abe36a31ffa03422420ea537bbe4f39a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97935759"
---
# <a name="azure-active-directory-governance-operations-reference-guide"></a>Azure Active Directory idare işlemleri başvuru kılavuzu

[Azure AD işlemler başvuru kılavuzu](active-directory-ops-guide-intro.md) 'nun bu bölümünde, erişimi değerlendirmek ve test etmek için gerçekleştirmeniz gereken denetimleri ve eylemleri, ortama yönelik ayrıcalıksız ve ayrıcalıklı kimlikleri, denetimi ve denetim değişikliklerini değerlendirmek için gerçekleştirmeniz gerekir.

> [!NOTE]
> Bu öneriler, yayımlama tarihi itibariyle geçerli olmakla kalmaz, zaman içinde değişebilir. Kuruluşlar, Microsoft ürün ve Hizmetleri zaman içinde geliştikçe idare uygulamalarını sürekli olarak değerlendirmelidir.

## <a name="key-operational-processes"></a>Anahtar işletimsel işlemler

### <a name="assign-owners-to-key-tasks"></a>Anahtar görevlere sahipler atama

Azure Active Directory Yönetimi, önemli işlem görevlerinin ve işlemlerin sürekli yürütülmesini gerektirir ve bu, bir dağıtım projesinin parçası olmayabilir. Ortamınızı iyileştirmek için bu görevleri ayarlamanız de önemlidir. Önemli görevler ve bunların önerilen sahipleri şunlardır:

| Görev | Sahip |
| :- | :- |
| SıEM sisteminde Azure AD denetim günlüklerini arşivleme | InfoSec Işlemler ekibi |
| Uyumsuz olarak yönetilen uygulamaları bulma | IAM Işlemler ekibi |
| Uygulamalara erişimi düzenli olarak gözden geçirin | InfoSec mimarisi ekibi |
| Dış kimliklere erişimi düzenli olarak gözden geçirin | InfoSec mimarisi ekibi |
| Ayrıcalıklı rollere sahip olan kişileri düzenli olarak gözden geçirin | InfoSec mimarisi ekibi |
| Ayrıcalıklı rolleri etkinleştirmek için güvenlik kapıları tanımlama | InfoSec mimarisi ekibi |
| İzin verdiği onayları düzenli olarak gözden geçirin | InfoSec mimarisi ekibi |
| Kuruluştaki çalışanlar temelinde uygulamalar ve kaynaklar için Katalog ve erişim paketleri tasarlama | Uygulama sahipleri |
| Kullanıcılara paketlere erişim atamak için güvenlik Ilkelerini tanımlama | InfoSec ekibi + uygulama sahipleri |
| İlkeler onay iş akışları içeriyorsa, iş akışı onaylarını düzenli olarak gözden geçirin | Uygulama sahipleri |
| Erişim gözden geçirmeleri kullanarak koşullu erişim ilkeleri gibi Güvenlik ilkelerindeki özel durumları gözden geçirin | InfoSec Işlemler ekibi |

Listenizi gözden geçirdikten sonra, sahibi eksik olan görevler için bir sahip atamanız veya yukarıdaki önerilere göre hizalanmamış olan sahiplerin sahipliğini ayarlamanız gerekebilir.

#### <a name="owner-recommended-reading"></a>Sahibi tarafından önerilen okuma

- [Azure Active Directory’de yönetici rolü atama](../roles/permissions-reference.md)
- [Azure’da idare](../../governance/index.yml)

### <a name="configuration-changes-testing"></a>Yapılandırma değişiklikleri testi

Bir paralel test kiracısında bir değişikliği dağıtmak için kullanıcıların hedef alt kümesini kullanıma alma gibi basit tekniklerden, test sırasında özel hususlar gerektiren değişiklikler vardır. Bir test stratejisi uygulamadıysanız, aşağıdaki tabloda yer alan yönergelere göre bir test yaklaşımı tanımlamanız gerekir:

| Senaryo| Öneri |
|-|-|
|Kimlik doğrulama türünü federe iken PHS/PTA veya tam tersi olarak değiştirme| Kimlik doğrulama türünü değiştirmenin etkilerini test etmek için [hazırlanan dağıtımı](../hybrid/how-to-connect-staged-rollout.md) kullanın.|
|Yeni bir koşullu erişim (CA) ilkesi veya kimlik koruma Ilkesi kullanıma alınıyor|Yeni bir koşullu erişim ilkesi oluşturun ve test kullanıcılarına atayın.|
|Uygulamanın test ortamını ekleme|Uygulamayı bir üretim ortamına ekleyin, Uygps panelinden gizleyin ve kalite güvencesi (QA) aşamasında kullanıcıları test etmek üzere atayın.|
|Eşitleme kurallarını değiştirme|Bir test Azure AD Connect içindeki değişiklikleri, hazırlama modu olarak da bilinen ve şu anda üretimde olan aynı yapılandırmayla gerçekleştirin ve CSExport sonuçlarını çözümleyin. Memnun kalmıyorsa, hazırlık sırasında üretime geçin.|
|Markalamayı değiştirme|Ayrı bir test kiracısında test edin.|
|Yeni bir özellik kullanıma alınıyor|Özellik, bir hedef kullanıcı kümesine yönelik kullanıma almayı destekliyorsa, pilot kullanıcıları belirleyin ve derleyin. Örneğin, self servis parola sıfırlama ve Multi-Factor Authentication belirli kullanıcıları veya grupları hedefleyebilir.|
|Şirket içi kimlik sağlayıcısından (IDP) bir uygulamayı Azure AD 'ye Active Directory, örneğin, Azure AD 'ye ver|Uygulama, örneğin Salesforce gibi birden çok IDP yapılandırmasını destekliyorsa, değişiklik penceresi sırasında Azure AD 'yi yapılandırın ve test edin (uygulamanın HRD sayfasını sağlaması durumunda). Uygulama birden çok IDPs 'yi desteklemiyorsa, bir değişiklik denetim penceresi ve program kapalı kalma süresi sırasında testi zamanlayın.|
|Dinamik grup kurallarını Güncelleştir|Yeni kurala sahip bir paralel dinamik grup oluşturun. Hesaplanan sonuca göre karşılaştırın, örneğin, PowerShell 'i aynı koşulla çalıştırın.<br>Test başarılı olursa, eski grubun kullanıldığı yerleri (mümkünse) değiştirin.|
|Ürün lisanslarını geçirme|[Azure Active Directory bir lisanslı gruptaki tek bir kullanıcı için lisansı değiştirme](../enterprise-users/licensing-groups-change-licenses.md)bölümüne bakın.|
|Yetkilendirme, verme, MFA gibi AD FS kuralları değiştirme|Kullanıcı alt kümesini hedeflemek için grup talebi kullanın.|
|AD FS kimlik doğrulama deneyimini veya benzer Grup genelinde değişiklikleri değiştirme|Aynı ana bilgisayar adına sahip bir paralel grup oluşturun, yapılandırma değişikliklerini uygulayın, Konaklar dosyası, NLB yönlendirme kuralları veya benzer yönlendirme kullanarak istemcilerden test edin.<br>Hedef platform HOSTS dosyalarını (örneğin, mobil cihazlar) desteklemiyorsa, Denetim değişikliği.|

## <a name="access-reviews"></a>Erişim gözden geçirmeleri

### <a name="access-reviews-to-applications"></a>Uygulamalara erişim gözden geçirmeleri

Zaman içinde, kullanıcılar farklı takımlar ve konumlar boyunca hareket ettikleri sürece kaynaklara erişim birikmesini sağlayabilir. Kaynak sahiplerinin uygulamalara düzenli olarak erişimini incelemesi ve kullanıcıların yaşam döngüsü boyunca artık gerekmeyen ayrıcalıkları kaldırması önemlidir. Azure AD [erişim gözden geçirmeleri](../governance/access-reviews-overview.md) , kuruluşların grup üyeliklerini verimli bir şekilde yönetmesine, kurumsal uygulamalara erişime ve rol atamalarına olanak tanır. Kaynak sahipleri, yalnızca doğru kişilerin erişmeye devam ettiğinden emin olmak için kullanıcıların erişimini düzenli olarak gözden geçirmelidir. İdeal olarak, bu görev için Azure AD erişim gözden geçirmeleri kullanmayı göz önünde bulundurmanız gerekir.

![Erişim İncelemeleri başlangıç sayfası](./media/active-directory-ops-guide/active-directory-ops-img15.png)

> [!NOTE]
> Erişim incelemeleriyle etkileşim kuran her kullanıcının ücretli Azure AD Premium P2 lisansı olması gerekir.

### <a name="access-reviews-to-external-identities"></a>Dış kimliklere erişim gözden geçirmeleri

Yalnızca gerekli olan süre boyunca gerekli olan kaynaklarla sınırlı dış kimliklere erişim sağlamak önemlidir. Tüm dış kimlikler ve uygulama erişimi için Azure AD [erişim gözden geçirmeleri](../governance/access-reviews-overview.md)kullanarak düzenli bir otomatik erişim gözden geçirme işlemi oluşturun. Şirket içi bir işlem zaten varsa, Azure AD erişim gözden geçirmeleri kullanmayı göz önünde bulundurun. Bir uygulama kullanımdan kalkdıktan veya artık kullanılmadıysa, uygulamaya erişimi olan tüm dış kimlikleri kaldırın.

> [!NOTE]
> Erişim incelemeleriyle etkileşim kuran her kullanıcının ücretli Azure AD Premium P2 lisansı olması gerekir.

## <a name="privileged-account-management"></a>Ayrıcalıklı hesap yönetimi

### <a name="privileged-account-usage"></a>Ayrıcalıklı hesap kullanımı

Saldırganlar genellikle hassas verilere ve sistemlere hızla erişim kazanmak için yönetici hesaplarını ve ayrıcalıklı erişim 'in diğer öğelerini hedefler. Ayrıcalıklı rollerine sahip olan kullanıcılar zaman içinde birikme eğilimiyle, yönetici erişiminin düzenli olarak incelenmesi ve yönetilmesi ve Azure AD ve Azure kaynaklarına tam zamanında ayrıcalıklı erişim sağlamak önemlidir.

Kuruluşunuzda ayrıcalıklı hesapları yönetmek için herhangi bir işlem yoksa veya şu anda hizmet ve kaynakları yönetmek için kendi normal kullanıcı hesaplarını kullanan yöneticileriniz varsa, örneğin normal günlük etkinlikler için bir tane olmak üzere ayrı hesapları kullanmaya başlamanız gerekir. diğer bir deyişle, ayrıcalıklı erişim için ve MFA ile yapılandırılır. Daha sonra, kuruluşunuzun bir Azure AD Premium P2 aboneliği varsa, hemen [Azure AD Privileged Identity Management](../privileged-identity-management/pim-configure.md#license-requirements) (PIM) dağıtmanız gerekir. Aynı belirteçte Ayrıca, bu ayrıcalıklı hesapları gözden geçirmeniz ve uygunsa [daha az ayrıcalıklı roller atamanız](../roles/security-planning.md) gerekir.

Ayrıcalıklı hesap yönetiminin uygulanması gereken başka bir yönü, bu hesaplar için el ile veya [otomatik olarak PIM aracılığıyla](../privileged-identity-management/pim-how-to-perform-security-review.md) [erişim gözden geçirmeleri](../governance/access-reviews-overview.md) tanımlıyor.

#### <a name="privileged-account-management-recommended-reading"></a>Ayrıcalıklı hesap yönetiminin kullanılması önerilir

- [Azure AD Privileged Identity Management roller](../privileged-identity-management/pim-roles.md)

### <a name="emergency-access-accounts"></a>Acil durum erişim hesapları

Kuruluşlar, şunun gibi kimlik doğrulama kesintileri gibi durumlarda Azure AD 'yi yönetmeye hazırlanmaya hazır olmak için [acil durum hesapları](../roles/security-emergency-access.md) oluşturmalıdır:

- Kimlik doğrulama altyapılarının kesinti bileşenleri (AD FS, şirket içi AD, MFA hizmeti)
- Yönetim personeli cirosu

Mevcut bireysel bir kullanıcının hesabını yönetici olarak oturum açmanıza veya etkinleştiremediği için kiracınızın yanlışlıkla kilitlenmemesini engellemek için, iki veya daha fazla acil durum hesabı oluşturmanız ve [Microsoft 'un en iyi yöntemleri](../roles/security-planning.md) ve çıkış [camı yordamlarına](../roles/security-planning.md#break-glass-what-to-do-in-an-emergency)göre uygulandığından ve hizalandıklarından emin olmanız gerekir.

### <a name="privileged-access-to-azure-ea-portal"></a>Azure EA portalına ayrıcalıklı erişim

[Azure kurumsal anlaşma (Azure EA) portalı](https://azure.microsoft.com/blog/create-enterprise-subscription-experience-in-azure-portal-public-preview/) , kuruluş içinde güçlü bir rol olan ana kurumsal anlaşma karşı Azure abonelikleri oluşturmanıza olanak sağlar. Azure AD 'nin yerine bu portalın oluşturulmasını önyüklemek yaygındır. bu nedenle, Azure AD kimliklerini kullanarak bunu kapatmak, portaldan kişisel hesapları kaldırmak, uygun temsilcinin yerinde olmasını sağlamak ve kilitleme riskini azaltmak için gereklidir.

Açık olması için, EA Portalı Yetkilendirme düzeyi Şu anda "karma mod" olarak ayarlandıysa, tüm [Microsoft HESAPLARıNı](https://support.skype.com/en/faq/FA12059/what-is-a-microsoft-account) EA portalındaki tüm ayrıcalıklı erişimlerden KALDıRMANıZ ve EA portalını yalnızca Azure AD hesaplarını kullanacak şekilde yapılandırmanız gerekir. EA Portalı temsilci rolleri yapılandırılmamışsa, departmanlar ve hesaplar için de temsilci roller bulmanız ve uygulamanız gerekir.

#### <a name="privileged-access-recommended-reading"></a>Ayrıcalıklı erişimin okunması önerilir

- [Azure Active Directory'de yönetici rolü izinleri](../roles/permissions-reference.md)

## <a name="entitlement-management"></a>Yetkilendirme yönetimi

[Yetkilendirme Yönetimi (EM)](../governance/entitlement-management-overview.md) , uygulama sahiplerinin kaynakları paketleyebilmesine ve bunları kuruluştaki belirli kişilere (iç ve dış) atamasına izin verir. EM, yönetim sağlamak, erişim sürelerini ayarlamak ve onay iş akışlarına izin vermek için idare ilkelerini korurken iş sahiplerine self servis kaydolma ve temsilciliğini sağlar. 

> [!NOTE]
> Azure AD yetkilendirme yönetimi için Azure AD Premium P2 lisansları gerekir.

## <a name="summary"></a>Özet

Güvenli bir kimlik yönetimi için sekiz yönü vardır. Bu liste, ayrıcalıklı olmayan ve ayrıcalıklı kimlikler, denetim ve ortamda Denetim değişiklikleri için verilen erişimi değerlendirmek ve test etmek için gerçekleştirmeniz gereken eylemleri belirlemenize yardımcı olur.

- Anahtar görevlere sahip atayın.
- Test stratejisi uygulayın.
- Grup üyeliklerini verimli bir şekilde yönetmek için Azure AD erişim Incelemelerini kullanın, kurumsal uygulamalara ve rol atamalarına erişin.
- Tüm dış kimlik ve uygulama erişimi türleri için düzenli, otomatik erişim gözden geçirme işlemi oluşturun.
- Yönetici erişimini düzenli aralıklarla gözden geçirmek ve yönetmek için bir erişim gözden geçirme işlemi oluşturun ve Azure AD ve Azure kaynaklarına tam zamanında ayrıcalıklı erişim sağlayın.
- Beklenmedik kesintiler için Azure AD 'yi yönetmeye hazırlanmaya hazır acil durum hesapları sağlayın.
- Azure EA portalına erişimi kilitle.
- Bir kaynak koleksiyonuna yönetilen erişim sağlamak için Yetkilendirme Yönetimi uygulayın.

## <a name="next-steps"></a>Sonraki adımlar

[Azure AD işletimsel denetimleri ve eylemleri](active-directory-ops-guide-ops.md)ile çalışmaya başlayın.