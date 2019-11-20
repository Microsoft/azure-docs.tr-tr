---
title: LinkedIn veri paylaşımı ve onayı-Azure Active Directory | Microsoft Docs
description: LinkedIn tümleştirmesinin Azure Active Directory ' deki Microsoft uygulamaları aracılığıyla verileri nasıl paylaştığını açıklar
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: a21c986ccfe96bae5d341e0ba2ee6622d998d076
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74025387"
---
# <a name="linkedin-account-connections-data-sharing-and-consent"></a>LinkedIn hesap bağlantıları veri paylaşımı ve onayı

Active Directory (Azure AD) kuruluşunuzdaki kullanıcıların Microsoft iş veya okul hesabını LinkedIn hesabıyla bağlamayı kabul etmek için izin verebilirsiniz. Bir kullanıcı hesaplarını bağladıktan sonra, LinkedIn 'deki bilgiler ve önemli noktalar bazı Microsoft uygulamaları ve Hizmetleri 'nde kullanılabilir. Ayrıca kullanıcılar, LinkedIn 'deki ağ deneyimlerini Microsoft 'tan alınan bilgilerle iyileşme ve zenginleştirmesi için de bekleyebilir.

Microsoft uygulamaları ve Hizmetleri 'nde LinkedIn bilgilerini görmek için, kullanıcıların kendi Microsoft ve LinkedIn hesaplarını bağlamaya izin verilmelidir. Kullanıcılardan Outlook, OneDrive veya SharePoint Online 'daki bir profil kartında birisinin LinkedIn bilgilerini görmek için hesaplarını ilk kez tıklamaları istenir. LinkedIn hesabı bağlantıları, deneyimi kabul edene ve hesaplarını bağlamaya kadar kullanıcılarınız için tam olarak etkinleştirilmez.

[!INCLUDE [active-directory-gdpr-note](../../../includes/gdpr-hybrid-note.md)]

## <a name="benefits-of-sharing-linkedin-information"></a>LinkedIn bilgilerini paylaşma avantajları

Microsoft uygulamaları ve Hizmetleri içindeki LinkedIn bilgilerine erişim sayesinde kullanıcılarınızın, kuruluşunuzun içindeki ve dışındaki iş arkadaşlarınızla, müşterilerle ve iş ortaklarıyla profesyonel ilişkiler oluşturmasını, bu kişilerle ve iş ortaklarına bağlanmasını kolaylaştırır. Yeni kullanıcılar iş arkadaşlarınızla bağlantı kurarak, bunlarla ilgili daha fazla bilgi edinmek ve daha fazla bilgiye kolayca erişmek yoluyla daha hızlı hızlanır. LinkedIn bilgilerinin Microsoft uygulamalarındaki profil kartında nasıl göründüğünü gösteren bir örnek aşağıda verilmiştir:

![Kuruluşunuzda LinkedIn tümleştirmesini etkinleştirme](./media/linkedin-user-consent/display-example.png)

## <a name="enable-and-announce-linkedin-integration"></a>LinkedIn Tümleştirmesini Etkinleştirme ve duyurma

Kuruluşunuzun ayarını yönetmek için bir Azure Active Directory yöneticisi olmanız gerekir. Bu uygulamayı tüm kullanıcılar için veya belirli bir kullanıcı kümesi için etkinleştirebilirsiniz.

1. Tümleştirmeyi etkinleştirmek veya devre dışı bırakmak için [Azure AD kuruluşunuz Için LinkedIn tümleştirmesi onay](linkedin-integration.md)bölümündeki adımları izleyin.
2. Kuruluşunuzda LinkedIn tümleştirmesini duyurdığınızda, kullanıcılarınızı [Microsoft uygulamaları ve Hizmetleri 'Ndeki LinkedIn bilgileri](https://support.office.com/article/about-linkedin-information-and-features-in-microsoft-apps-and-services-dc81cc70-4d64-4755-9f1c-b9536e34d381)hakkında SSS bölümüne getirin. Makale, LinkedIn bilgisinin nerede görüntüleneceği, hesapların nasıl bağlanacağı ve daha birçok konuda bilgi sağlar.

## <a name="user-consent-for-data-access-in-microsoft-and-linkedin"></a>Microsoft ve LinkedIn 'de veri erişimi için Kullanıcı onayı

LinkedIn 'ten erişilen veriler, Microsoft hizmetlerinde kalıcı olarak depolanmaz. Microsoft 'tan erişilen veriler, kişiler hariç, LinkedIn ile kalıcı olarak depolanmaz. Microsoft kişileri, [içeri aktarılan kişileri LinkedIn 'ten silme](https://www.linkedin.com/help/linkedin/answer/43377)bölümünde açıklandığı gibi, kullanıcılar onları kaldırana kadar LinkedIn 'de depolanır.

Kullanıcılar hesaplarının hesaplarını, bilgilerini ve içgörüleri, profil kartı gibi bazı Microsoft uygulamalarında kullanılabilir. Ayrıca kullanıcılar, LinkedIn 'deki ağ deneyimlerini Microsoft 'tan alınan bilgilerle iyileşme ve zenginleştirmesi için de bekleyebilir.
Kuruluşunuzdaki kullanıcılar LinkedIn ve Microsoft iş veya okul hesaplarını bağladığınızda iki seçeneğe sahiptir:

* Verilere her iki hesaptan de erişilmesine izin verin. Bu, Microsoft veya iş hesabının LinkedIn hesabından verilere erişmesi ve [LinkedIn hesaplarının Microsoft iş veya okul hesabından verilere erişmesi](https://www.linkedin.com/help/linkedin/answer/84077)için izin veren anlamına gelir.
* Microsoft iş ve okul hesabı tarafından yalnızca LinkedIn verilerine erişim izni verin.

Kullanıcılar hesapların bağlantısını kesebilir ve veri erişimi izinlerini dilediğiniz zaman kaldırabilir ve kullanıcılar, profillerinin Microsoft uygulamalarında görüntülenip görüntülenemeyeceğini de içeren [kendi LinkedIn profillerinin nasıl görüntülendiğini denetleyebilir](https://www.linkedin.com/help/linkedin/answer/83).

### <a name="linkedin-account-data"></a>LinkedIn hesap verileri

Microsoft ve LinkedIn hesaplarınızı bağladığınızda, LinkedIn 'in Microsoft 'a aşağıdaki verileri sağlamasına izin verin:

* Profil verileri-LinkedIn profilinizi, iletişim bilgilerini ve [LinkedIn profilinizde](https://www.linkedin.com/help/linkedin/answer/15493)başkalarıyla paylaştığınız bilgileri içerir.
* İlgi alanları, ilgilendiğiniz kişiler ve konular, kurslar grupları ve beğendiğiniz içerikler gibi LinkedIn 'teki ilgi alanlarınızı içerir.
* Abonelik verileri-ilişkili verilerle birlikte LinkedIn uygulamalarına ve hizmetlerine yönelik abonelikleri içerir. 
* Bağlantı verileri-1. düzey bağlantılarınızın profilleri ve iletişim bilgileri dahil olmak üzere [LinkedIn ağınızı](https://www.linkedin.com/help/linkedin/answer/110) içerir.

LinkedIn 'ten erişilen veriler, Microsoft hizmetlerinde kalıcı olarak depolanmaz. Microsoft 'un kişisel veri kullanımı hakkında daha fazla bilgi için bkz. [Microsoft gizlilik bildirimi](https://privacy.microsoft.com/privacystatement/).

### <a name="microsoft-work-or-school-account-data"></a>Microsoft iş veya okul hesabı verileri

Microsoft ve LinkedIn hesaplarınızı bağladığınızda Microsoft 'un LinkedIn 'e aşağıdaki verileri sağlamasına izin verebilirsiniz:

* Profil verileri-ilk adınız, soyadı, profil fotoğrafınız, e-posta adresiniz, yöneticiniz ve yönettiğiniz kişiler gibi bilgileri içerir.
* Takvim verileri-takvimlerinizde, saatlerinizde, konumlarında ve katılımcıların iletişim bilgilerinde toplantılar içerir. Toplantı hakkındaki bilgiler, Gündem, içerik veya toplantı başlığı gibi takvim verilerine dahil değildir.
* İlgi alanları verileri-Cortana ve Bing for Business gibi Microsoft hizmetlerini kullanmanıza göre hesabınızla ilişkili olan ilgi alanlarınızı içerir.
* Abonelik verileri-kuruluşunuz tarafından sunulan abonelikleri, Office 365 gibi Microsoft uygulamalarına ve hizmetlerine dahil eder.
* Kişiler verileri-Outlook, Skype ve diğer Microsoft hesabı hizmetlerinde, sık iletişim kurduğunuz veya işbirliği yaptığınız kişilerin iletişim bilgileri de dahil olmak üzere kişi listelerini içerir. Kişiler, LinkedIn tarafından düzenli olarak içeri aktarılır, depolanır ve kullanılır. Örneğin, bağlantılar önermek, kişilerin düzenlenmesine yardımcı olur ve kişiler hakkındaki güncelleştirmeleri gösterebilirsiniz.

Microsoft 'tan erişilen veriler, kişiler hariç, LinkedIn ile kalıcı olarak depolanmaz. Microsoft kişileri, kullanıcılar onları kaldırana kadar LinkedIn 'e depolanır. [İçeri aktarılan kişileri LinkedIn 'den silme](https://www.linkedin.com/help/linkedin/answer/43377)hakkında daha fazla bilgi edinin.

LinkedIn 'in kişisel verileri kullanımı hakkında daha fazla bilgi için bkz. [LinkedIn Gizlilik ilkesi](https://www.linkedin.com/legal/privacy-policy). LinkedIn Hizmetleri, veri aktarımı ve depolama için, veriler Avrupa birleşimden Birleşik Devletler ve geri akabilir ve gizlilik, [Avrupa Birliği veri aktarımları](https://www.linkedin.com/help/linkedin/answer/62533)bölümünde açıklandığı gibi korunur.

## <a name="next-steps"></a>Sonraki adımlar

* [İş veya okul hesabınızla Microsoft uygulamalarında LinkedIn](https://www.linkedin.com/help/linkedin/answer/84077)
