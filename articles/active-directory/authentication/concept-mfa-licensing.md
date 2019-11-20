---
title: Azure MFA sürümleri ve tüketim planları-Azure Active Directory
description: Multi-Factor Authentication istemcisi ve kullanılabilir farklı yöntemler ve sürümler hakkında bilgiler.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 78e34e0433dd362150680046c2f0ca53996063c4
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73151754"
---
# <a name="how-to-get-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication alma

Hesaplarınız korunurken, iki adımlı doğrulama, kuruluşunuz genelinde standart olmalıdır. Bu özellik özellikle kaynaklara ayrıcalıklı erişimi olan hesaplar için önemlidir. Bu nedenle, Microsoft, ek maliyet olmadan Office 365 ve Azure Active Directory (Azure AD) yöneticilerine temel iki adımlı doğrulama özellikleri sunar. Yöneticileriniz için özellikleri yükseltmek veya iki aşamalı doğrulamayı kullanıcılarınızın geri kalanına genişletmek istiyorsanız, Azure Multi-Factor Authentication çeşitli yollarla satın alabilirsiniz.

> [!IMPORTANT]
> Bu makalede, Azure Multi-Factor Authentication satın almanın farklı yollarını anlamanıza yardımcı olacak bir kılavuz olması amaçlanmıştır. Fiyatlandırma ve faturalandırma hakkında belirli Ayrıntılar için [Multi-Factor Authentication fiyatlandırma sayfasına](https://azure.microsoft.com/pricing/details/multi-factor-authentication/)her zaman başvurmalısınız.
>

## <a name="available-versions-of-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication 'nin kullanılabilir sürümleri

Aşağıdaki tabloda Multi-Factor Authentication sürümleri arasındaki farklılıklar açıklanmaktadır:

| Sürüm | Açıklama |
| --- | --- |
| Ücretsiz seçenek | Azure AD 'nin ücretsiz avantajlarından yararlanan müşteriler, ortamınızda Multi-Factor Authentication 'ı etkinleştirmek için [güvenlik varsayılanlarını](../conditional-access/concept-conditional-access-security-defaults.md) kullanabilir. |
| Office 365 için Multi-Factor Authentication | Bu sürüm, Office 365 veya Microsoft 365 portalından yönetilir. Yöneticiler, [iki adımlı doğrulamayla Office 365 kaynaklarını güvenli hale](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6)getirebilirsiniz. Bu sürüm, Office 365 aboneliğinin bir parçasıdır. |
| Azure AD yöneticileri için Multi-Factor Authentication | Azure AD kiracılarında Azure AD Genel Yöneticisi rolüne atanan kullanıcılar, ek bir ücret ödemeden iki aşamalı doğrulamayı etkinleştirebilir. |
| Azure Multi-Factor Authentication | Genellikle "Full" sürümü olarak anılan Azure Multi-Factor Authentication, zenginest özellik kümesini sunar. [Azure Portal](https://portal.azure.com), gelişmiş raporlama ve bir dizi şirket içi ve bulut uygulaması için destek aracılığıyla ek yapılandırma seçenekleri sunar. Azure Multi-Factor Authentication, [Azure Active Directory Premium](https://www.microsoft.com/cloud-platform/azure-active-directory-features) ve [Microsoft 365 iş](https://www.microsoft.com/microsoft-365/business)bir özelliğidir. |

> [!NOTE]
> Yeni müşteriler artık 1 Eylül 2018 ' de geçerli olan tek başına bir teklif olarak Azure Multi-Factor Authentication satın alabilirler. Multi-Factor Authentication, Azure AD Premium veya Microsoft 365 İş lisanslarında bir özellik olarak kullanılabilir olmaya devam edecektir.

## <a name="feature-comparison-of-versions"></a>Sürümlerin Özellik Karşılaştırması

Aşağıdaki tabloda, çeşitli Azure Multi-Factor Authentication sürümlerinde kullanılabilen özelliklerin bir listesi verilmiştir.

> [!NOTE]
> Bu karşılaştırma tablosu Multi-Factor Authentication her bir sürümünün parçası olan özellikleri açıklamaktadır. Tam Azure Multi-Factor Authentication hizmetiniz varsa, [MFA 'yı bulutta mi yoksa Şirket IÇINDE MFA](concept-mfa-whichversion.md)mı kullandığınıza bağlı olarak bazı özellikler kullanılamayabilir.
>

| Özellik | Office 365 için Multi-Factor Authentication | Azure AD yöneticileri için Multi-Factor Authentication | Azure Multi-Factor Authentication | Güvenlik varsayılanları | 
| --- |:---:|:---:|:---:|:---:|
| MFA ile Azure AD yönetici hesaplarını koruma |● |● (Yalnızca Azure AD Genel yönetici hesapları) |● |● |
| İkinci bir faktör olarak mobil uygulama |● |● |● |● |
| İkinci bir faktör olarak telefon araması |● |● |● |   |
| İkinci bir faktör olarak SMS |● |● |● |   |
| MFA 'yı desteklemeyen istemciler için uygulama parolaları |● |● |● |   |
| Doğrulama yöntemleri üzerinde yönetici denetimi |● |● |● |   |
| MFA ile yönetici olmayan hesapları koruma |● | |● |● |
| PIN modu | | |● |   |
| Sahtekarlık uyarısı | | |● |   |
| MFA Raporları | | |● |   |
| Bir Kerelik Atlatma | | |● |   |
| Telefon aramaları için özel karşılama | | |● |   |
| Telefon aramaları için özel arayan KIMLIĞI | | |● |   |
| Güvenilen IP'ler | | |● |   |
| Güvenilen cihazlar için MFA hatırlama |● |● |● |   |
| Şirket içi uygulamalar için MFA | | |● |   |

> [!IMPORTANT]
> Mart 2019 ' den itibaren telefon araması seçenekleri ücretsiz/deneme Azure AD kiracılarında MFA ve SSPR kullanıcıları tarafından kullanılamaz. SMS iletileri bu değişiklikten etkilenmez. Telefon araması, ücretli Azure AD kiracılarındaki kullanıcılar için kullanılabilir olmaya devam edecektir. Bu değişiklik yalnızca ücretsiz/deneme Azure AD kiracılarını etkiler.

## <a name="how-to-turn-on-azure-multi-factor-authentication-for-azure-ad-administrators"></a>Azure AD yöneticileri için Azure Multi-Factor Authentication 'yi etkinleştirme

Azure AD kiracılarında genel yönetici rolüne atanan kullanıcılar, Azure AD Genel yönetici hesapları için ek bir ücret ödemeden iki aşamalı doğrulamayı etkinleştirebilir. Bir Microsoft hesabı kullanıyorsanız, [iki adımlı doğrulama hakkında](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification)Microsoft hesabı destek makalesinde bulunan kılavuzdan yararlanarak Multi-Factor Authentication için kaydolabilirsiniz. Bir Microsoft hesabı kullanmıyorsanız, [bir kullanıcı veya grup için iki adımlı doğrulama gerektirme](howto-mfa-userstates.md)makalesinde bulunan Kılavuzu kullanarak genel Yöneticiler için Multi-Factor Authentication 'ı açın.

## <a name="how-to-purchase-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication satın alma

Azure Active Directory Premium gibi Azure Multi-Factor Authentication veya Azure AD Premium ya da koşullu erişimi içeren bir lisans paketi satın alın ve bu lisansları Azure Active Directory Kullanıcılarınıza atayın.

### <a name="consumption-based-licensing"></a>Tüketim tabanlı lisanslama

Tüketim tabanlı lisanslama, 1 Eylül 2018 ' de geçerli olan yeni müşteriler için artık kullanılamaz.

1 Eylül 2018 ' den itibaren yeni kimlik doğrulama sağlayıcıları artık oluşturulmayabilir. Mevcut kimlik doğrulama sağlayıcıları çalışmaya ve güncelleştirilmesine devam edebilir. Multi-Factor Authentication, Azure AD Premium lisanlarında kullanılabilir bir özellik olmaya devam edecektir.

Azure Multi-Factor Authentication sağlayıcısı kullanırken, Azure aboneliğinizde Faturalandırılan iki kullanım modeli bulunur:

1. **Etkin Kullanıcı başına** -kimlik doğrulamasına düzenli olarak ihtiyaç duyan sabit sayıda çalışan için iki aşamalı doğrulamayı etkinleştirmek isteyen kuruluşlar için. Kullanıcı başına faturalandırma, Azure AD kiracınızda ve Azure MFA sunucunuzda MFA için etkinleştirilen kullanıcı sayısına bağlıdır. Kullanıcılar hem Azure AD hem de Azure MFA sunucusunda MFA için etkinleştirildiyse ve etki alanı eşitleme (Azure AD Connect) etkinleştirilirse, daha büyük kullanıcı kümesini sayıyoruz. Etki alanı eşitleme etkin değilse, Azure AD ve Azure MFA sunucusu 'nda MFA için etkinleştirilen tüm kullanıcıların toplamını sayıyoruz. Faturalandırma, eşit olarak dağıtılır ve her gün ticari sisteme bildirilir.

   > [!NOTE]
   > Faturalandırma örneği 1: günümüzde MFA için etkinleştirilen 5.000 kullanıcı var. MFA sistemi bu sayıyı 31 ' i böler ve bu güne ait 161,29 kullanıcısı raporlar. Yarın, 15 daha fazla kullanıcı etkinleştirin, bu nedenle MFA sistemi bu güne ait 161,77 kullanıcıyı raporlar. Faturalandırma döngüsünün sonuna kadar, Azure aboneliğinize göre faturalandırılan toplam kullanıcı sayısı 5.000 ' e kadar bir çözüm ekler.
   >
   > Faturalandırma örneği 2: lisansı ve kullanıcıları olmayan kullanıcıların bir karışımına sahip olursunuz. bu nedenle, farkı sağlamak için Kullanıcı başına Azure MFA sağlayıcısına sahip olursunuz. Kiracınızda 4.500 Enterprise Mobility + Security lisans vardır ancak MFA için etkinleştirilen 5.000 kullanıcı. Azure aboneliğiniz, 500 Kullanıcı için faturalandırılır, eşit olarak dağıtılır ve 16,13 Kullanıcı olarak günlük raporlanır.
   >

1. **Kimlik doğrulaması başına** -daha seyrek kimlik doğrulamasına ihtiyaç duyan büyük bir Kullanıcı grubu için iki aşamalı doğrulamayı etkinleştirmek isteyen kuruluşlar için. Faturalandırma, bu doğrulamanın başarılı veya reddedilmiş olmasına bakılmaksızın iki adımlı doğrulama isteklerinin sayısını temel alır. Bu Faturalandırma, Azure kullanım deyiminizde 10 kimlik doğrulama paketlerinde görünür ve günlük olarak bildirilir.

   > [!NOTE]
   > Faturalandırma örnek 3: bugün, Azure MFA hizmeti 3.105 2 adımlı doğrulama istekleri aldı. Azure aboneliğiniz 310,5 kimlik doğrulama paketi için faturalandırılır.
   >

Lisanslarınızın olabileceğini, ancak hala tüketim tabanlı yapılandırma için faturalandırılırsanız emin olmanız önemlidir. Kimlik doğrulama başına Azure MFA sağlayıcısı ayarlarsanız, lisanslarına sahip kullanıcılar tarafından yapılan istekler bile dahil olmak üzere her iki adımlı doğrulama isteği için faturalandırılırsınız. Azure AD kiracınızla bağlantılı olmayan bir etki alanında Kullanıcı başına Azure MFA sağlayıcısı ayarlarsanız, kullanıcılarınız Azure AD üzerinde lisanslarına sahip olsa bile etkin kullanıcı başına faturalandırılırsınız.

## <a name="next-steps"></a>Sonraki adımlar

- Daha fazla fiyatlandırma ayrıntısı için bkz. [Azure MFA fiyatlandırması](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).
