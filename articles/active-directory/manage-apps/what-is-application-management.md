---
title: Azure Active Directory ile Uygulamaları Yönetme | Microsoft Docs
description: Bu makalede, şirket içi, bulut ve SaaS uygulamalarınızla Azure Active Directory tümleştirme avantajları açıklanmaktadır.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: overview
ms.workload: identity
ms.date: 06/05/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5d1ebb4b094ab5c03343486e7af6624273288453
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68421212"
---
# <a name="application-management-with-azure-active-directory"></a>Azure Active Directory ile uygulama yönetimi

Azure Active Directory (Azure AD), bulut ve şirket içi uygulamalarınız için tek bir kimlik sistemi sağlayarak uygulamalarınızı yönetmenin yolunu basitleştirir. Hizmet olarak yazılım (SaaS) uygulamaları, şirket içi uygulamalar ve iş kolu (LOB) uygulamalarını Azure AD 'ye ekleyebilirsiniz. Ardından kullanıcılar bir kez oturum açarak Office 365 ve Microsoft 'un diğer iş uygulamalarıyla birlikte bu uygulamalara güvenli ve sorunsuz bir şekilde erişin. [Kullanıcı sağlamayı otomatikleştirerek](user-provisioning.md)yönetim maliyetlerini azaltabilirsiniz. Ayrıca, güvenli uygulama erişimi sağlamak için Multi-Factor Authentication ve koşullu erişim ilkelerini de kullanabilirsiniz.

![Azure AD aracılığıyla federe olan uygulamaları gösteren diyagram](media/what-is-application-management/app-management-overview.png)

## <a name="why-manage-applications-with-a-cloud-solution"></a>Neden bir bulut çözümüyle uygulamalar yönetilmelidir?

Kuruluşların çoğu zaman kullanıcıların işlerini yapmak için bağımlı oldukları yüzlerce uygulaması vardır. Kullanıcılar birçok cihaz ve konumdan bu uygulamalara erişir. Her gün yeni uygulamalar eklenir ve geliştirilir. Bu kadar çok uygulama ve erişim noktası sayesinde, tüm uygulamalara Kullanıcı erişimini yönetmek için bulut tabanlı bir çözüm kullanmak her zamankinden daha önemlidir.

## <a name="what-types-of-applications-can-i-integrate-with-azure-ad"></a>Azure AD ile tümleştirilebilen uygulama türleri nelerdir?

**Kurumsal uygulamalarınıza** ekleyebileceğiniz ve Azure AD ile yönetebileceğiniz dört ana uygulama türü vardır:

- Azure AD **Galeri uygulamaları** – Azure AD, Azure AD ile çoklu oturum açma için önceden tümleştirilmiş binlerce uygulamayı içeren bir galeriye sahiptir. Galeride kuruluşunuzun kullandığı uygulamaların bazıları da mevcuttur. [Uygulama tümleştirmenizi planlama](plan-an-application-integration.md)veya [SaaS uygulama öğreticilerinde](https://docs.microsoft.com/azure/active-directory/saas-apps/)tek tek uygulamalar için ayrıntılı tümleştirme adımları alma hakkında bilgi edinin.

- **Uygulama proxy 'si ile şirket içi uygulamalar** – Azure AD uygulama ara sunucusu ile şirket içi Web UYGULAMALARıNıZı Azure AD ile tümleştirebilir ve çoklu oturum açma desteği sağlayabilirsiniz. Ardından, son kullanıcılar, şirket içi Web uygulamalarınıza Office 365 ve diğer SaaS uygulamalarına erişen şekilde erişebilirler. [Uygulama proxy 'sini kullanmayı ve nasıl çalıştığını öğrenin](what-is-application-proxy.md).

- **Özel geliştirmiş uygulamalar** – kendi iş kolu uygulamalarınızı oluştururken, çoklu oturum açmayı desteklemek Için BUNLARı Azure AD ile tümleştirebilirsiniz. Uygulamanızı Azure AD 'ye kaydederek, uygulamanın kimlik doğrulama ilkesi üzerinde denetiminiz vardır. Daha fazla bilgi için bkz. [geliştiricilere yönelik kılavuz](developer-guidance-for-integrating-applications.md).

- **Galeri olmayan uygulamalar** – kendi uygulamalarınızı getirin! Azure AD 'ye ekleyerek diğer uygulamalar için çoklu oturum açmayı destekler. İstediğiniz herhangi bir web bağlantısını veya bir Kullanıcı adı ve parola görüntüleyen herhangi bir uygulamayı tümleştirebilir, SAML veya OpenID Connect protokollerini destekler ya da SCıM 'yi destekler. Daha fazla bilgi için bkz. [Galeri dışı uygulamalar için çoklu oturum açmayı yapılandırma](configure-single-sign-on-non-gallery-applications.md).

## <a name="manage-risk-with-conditional-access-policies"></a>Koşullu erişim ilkeleriyle riskleri yönetme

[Koşullu erişim](https://docs.microsoft.com/azure/active-directory/conditional-access/overview) Ile Azure AD çoklu oturum açma (SSO), uygulamalara erişmek için yüksek düzeyde güvenlik sağlar. Güvenlik özellikleri, bulut ölçeğinde kimlik koruması, risk tabanlı erişim denetimi, yerel Multi-Factor Authentication ve koşullu erişim ilkelerini içerir. Bu özellikler, uygulamalara veya yüksek düzeyde güvenlik gereken gruplara göre ayrıntılı denetim ilkelerine olanak sağlar.

## <a name="improve-productivity-with-single-sign-on"></a>Çoklu oturum açma ile üretkenliği artırma

Uygulamalar ve Office 365 arasında çoklu oturum açma (SSO) etkinleştirildiğinde, oturum açma istemleri azaltılarak veya ortadan kaldırılarak mevcut kullanıcılar için üstün bir oturum açma deneyimi sağlanır. Birden çok istem veya birden çok parolayı yönetme gereksinimi olmadan kullanıcının ortamı çok daha bütünlüklü ve daha az rahatsız edici olur. İş grubu, self servis ve dinamik üyelik aracılığıyla erişimi yönetebilir ve onaylayabilir. İşletmede doğru kişilerin bir uygulamaya erişmesine izin verilmesi, kimlik sisteminin güvenliğini artırır.

SSO, güvenliği artırır. *Çoklu oturum açma olmadan* yöneticilerin her bir bireysel uygulama için kullanıcı hesapları oluşturması ve güncelleştirmesi gerekir; bu da zaman alır. Ayrıca kullanıcıların uygulamalarına erişmek için birden çok kimlik bilgisini izlemesi gerekir. Sonuç olarak kullanıcılar, parolalarını yazma veya başka parola yönetim çözümlerini kullanma eğilimindedir; bu da veri güvenliği riskleri oluşturur. [Çoklu oturum açma hakkında daha fazla bilgi edinin](what-is-single-sign-on.md).

## <a name="address-governance-and-compliance"></a>İdare ve uyumu ele alma

Azure AD ile, Güvenlik Bilgileri ve Olay Yönetimi (SIEM) araçlarından yararlanan raporlar aracılığıyla uygulama oturum açma işlemlerini izleyebilirsiniz. Portaldan veya API’lerden raporlara erişebilirsiniz. Uygulamalarınıza kimlerin erişiminin olduğunu programlama yoluyla denetleyin ve erişim gözden geçirmeleri yoluyla etkin olmayan kullanıcılara erişimi kaldırın.

## <a name="manage-costs"></a>Maliyetleri yönetme

Azure AD’ye geçirerek maliyet tasarrufu yapabilir ve şirket içi altyapınızı yönetme zahmetini ortadan kaldırabilirsiniz. Azure AD, uygulamalara self servis erişimi de sağlar; böylece hem yöneticiler hem de kullanıcılar için zaman tasarrufu sağlanır. Çoklu oturum açma, uygulamaya özgü parolaları ortadan kaldırır. Bir kez oturum açma, uygulamalar için parola sıfırlama ve parolalar alınırken oluşan üretkenlik kaybı ile ilgili maliyetlerden tasarruf sağlar.

## <a name="next-steps"></a>Sonraki adımlar

- [Uygulama proxy nedir?](what-is-application-proxy.md)
- [Hızlı Başlangıç: Azure AD kiracınıza Galeri uygulaması ekleme](add-application-portal.md)
