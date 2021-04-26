---
title: Microsoft Identity platformu ile tümleştirme | Mavisi
titleSuffix: Microsoft identity platform
description: Uygulamanızı Microsoft Identity platformu ile tümleştirmeyle ilgili avantajları öğrenin ve Basitleştirilmiş oturum açma, kimlik yönetimi, çok faktörlü kimlik doğrulaması ve erişim denetimi gibi özelliklerle ilgili kaynakları edinin.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/01/2020
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev, seoapril2019
ROBOTS: NOINDEX
ms.openlocfilehash: b7ee283ff61753a060e49a3340cd0a795b04faf9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98755938"
---
# <a name="integrating-with-the-microsoft-identity-platform"></a>Microsoft Identity platformu ile tümleştirme

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Bu makalede, uygulamanızı Microsoft Identity platformu ile tümleştirme ve tümleştirme için kaynaklar alma avantajları hakkında bilgi edinirsiniz. Microsoft Identity platform ve Azure Active Directory (AD), kuruluşlara bulut uygulamaları için kurumsal düzeyde kimlik yönetimi sağlar. Microsoft Identity platform tümleştirmesi kullanıcılarınıza kolay bir oturum açma deneyimi sağlar ve uygulamanızın BT ilkesine uymasını sağlar.

## <a name="how-to-integrate"></a>Nasıl tümleştirilir

Uygulamanızın Microsoft Identity platformu ile tümleştirilmesine yönelik çeşitli yollar vardır. Uygulamanız için uygun olan bu senaryolardan çok sayıda veya daha az yararlanın.

### <a name="support-the-microsoft-identity-platform-as-a-way-to-sign-in-to-your-application"></a>Uygulamanızda oturum açmak için bir yol olarak Microsoft Identity platformunu destekleme

**Oturum açma ve destek maliyetlerini azaltma.** Uygulamanızda oturum açmak için Microsoft Identity platformunu kullanarak, kullanıcılarınız hatırlayamayacak bir ad ve parolaya sahip olmayacaktır. Bir geliştirici olarak depolamak ve korumak için bir parola daha az olacaktır. Unutulmuş parola sıfırlamaları işlemek gerekmez, tek başına önemli bir tasarruf olabilir. Microsoft Identity platform, Microsoft 365 ve Microsoft Azure de dahil olmak üzere dünyanın en popüler bulut uygulamaları için oturum açın. Milyonlarca kuruluşun yüzlerce milyonlarca kullanıcı sayesinde, kullanıcılarınız Microsoft Identity platformunda zaten oturum açtı. [Microsoft Identity Platform oturum açma desteği ekleme](./authentication-vs-authorization.md)hakkında daha fazla bilgi edinin.

**Uygulamanıza kaydolmayı kolaylaştırın.**  Uygulamanıza kaydolma sırasında Microsoft Identity platformu, kayıt formunuzu önceden doldurabilmeniz veya tamamen ortadan kaldırabilmeniz için bir kullanıcı hakkında önemli bilgiler gönderebilir. Kullanıcılar, sosyal medya ve mobil uygulamalarda bulunanlara benzer tanıdık bir onay deneyimi aracılığıyla Azure AD hesabını kullanarak uygulamanıza kaydolabilir. Herhangi bir Kullanıcı, BT katılımı gerekmeden Microsoft Identity platformu ile tümleştirilmiş bir uygulamaya kaydolabilir ve oturum açabilir. [Azure AD hesabı oturum açma için uygulamanızı imzalama](../../app-service/configure-authentication-provider-aad.md)hakkında daha fazla bilgi edinin.

### <a name="browse-for-users-manage-user-provisioning-and-control-access-to-your-application"></a>Kullanıcılara gidin, Kullanıcı sağlamasını yönetin ve uygulamanıza erişimi denetleyin

**Dizindeki kullanıcılara gözatın.**  Kullanıcıların e-posta adresleri yazmalarını istemek yerine, kullanıcılar tarafından, kuruluştaki diğer kişileri aramasına ve bunlara gözatmasına yardımcı olmak için Microsoft Graph API 'sini kullanın. Kullanıcılar, kuruluş hiyerarşisinin ayrıntılarını görüntüleme dahil olmak üzere tanıdık bir adres defteri stili arabirimi kullanarak gözatabilirler. [Microsoft Graph API 'si](/graph/overview)hakkında daha fazla bilgi edinin.

**Müşterinizin zaten yönettiği Active Directory grupları ve dağıtım listelerini yeniden kullanın.**  Azure AD, müşterinizin e-posta dağıtımı ve erişim yönetimi için zaten kullandığı grupları içerir. Microsoft Graph API 'sini kullanarak, müşterinizin uygulamanızda ayrı gruplar kümesi oluşturmasını ve yönetmesini istemek yerine bu grupları yeniden kullanın. Grup bilgileri de uygulamanıza oturum açma belirteçlerinde gönderilebilir. [Microsoft Graph API 'si](/graph/overview)hakkında daha fazla bilgi edinin.

**Uygulamanıza kimlerin erişebileceğini denetlemek için Microsoft Identity platformunu kullanın.**  Azure AD 'deki Yöneticiler ve uygulama sahipleri, belirli kullanıcılara ve gruplara uygulamalara erişim atayabilir. Microsoft Graph API 'sini kullanarak, bu listeyi okuyabilir ve uygulama içinde kaynakların sağlanması ve sağlanması ve erişiminin kaldırılması denetlemek için kullanabilirsiniz.

**Rol tabanlı Access Control için Microsoft Identity platformunu kullanın.**  Yöneticiler ve uygulama sahipleri, uygulamanızı Microsoft Identity platform 'a kaydettiğinizde tanımladığınız rollere kullanıcılar ve gruplar atayabilir. Rol bilgileri, uygulamanıza oturum açma belirteçlerinde gönderilir ve ayrıca Microsoft Graph API kullanılarak okunabilir. [Yetkilendirme Için Microsoft Identity platformu kullanma](https://cloudblogs.microsoft.com/enterprisemobility/2014/12/18/azure-active-directory-now-with-group-claims-and-application-roles/)hakkında daha fazla bilgi edinin.

### <a name="get-access-to-users-profile-calendar-email-contacts-files-and-more"></a>Kullanıcıların profiline, takvimine, e-postaya, kişilere, dosyalarına ve daha fazlasına erişim sağlayın

**Microsoft Identity platform, Microsoft 365 ve diğer Microsoft iş hizmetleri için yetkilendirme sunucusudur.**  Uygulamanızda oturum açmak için Microsoft Identity platformunu destek, veya OAuth 2,0 kullanarak geçerli kullanıcı hesaplarınızı Azure AD Kullanıcı hesaplarına bağlamayı destekliyorsa, kullanıcının profiline, takvimine, e-postasına, kişilerine, dosyalarına ve diğer bilgilere okuma ve yazma erişimi isteyebilirsiniz. Kullanıcı takvimine olayları sorunsuzca yazabilir ve OneDrive 'a dosyaları okuyabilir veya yazabilirsiniz. [Microsoft 365 API 'leri](/graph/overview)hakkında daha fazla bilgi edinin.

### <a name="promote-your-application-in-the-azure-and-microsoft-365-marketplaces"></a>Uygulamanızı Azure 'da yükseltme ve Microsoft 365 pazar yerleri

**Uygulamanızı zaten Azure AD kullanan milyonlarca kuruluşa yükseltin.**  Bu markette arama yapan ve gözatabilen kullanıcılar zaten bir veya daha fazla bulut hizmeti kullanıyor ve bunları nitelikli bulut hizmeti müşterilerine yapıyor. [Azure Marketi](https://azure.microsoft.com/marketplace/partner-program/)'nde uygulamanızı yükseltme hakkında daha fazla bilgi edinin.

**Kullanıcılar uygulamanıza kaydolup Azure AD erişim panelinde ve uygulama başlatıcısı Microsoft 365 görünür.**  Kullanıcılar daha sonra uygulamanıza hızlı ve kolay bir şekilde geri dönebilir ve kullanıcı etkileşimini geliştirir. [Azure AD erişim paneli](../user-help/my-apps-portal-end-user-access.md)hakkında daha fazla bilgi edinin.

### <a name="secure-device-to-service-and-service-to-service-communication"></a>Cihazdan hizmete ve hizmetten hizmete iletişimi güvenli hale getirme

**Hizmet ve cihazların kimlik yönetimi için Microsoft Identity platform kullanımı, yazmanız gereken kodu azaltır ve erişimi yönetmesini sağlar.**  Hizmet ve cihazlar, OAuth kullanarak Microsoft Identity platformundan belirteçler alabilir ve bu belirteçleri Web API 'Lerine erişmek için kullanabilir. Microsoft Identity platformunu kullanarak, karmaşık kimlik doğrulama kodu yazmadan kaçınabilirsiniz. Hizmet ve cihazların kimlikleri Azure AD 'de depolandığından, bunu uygulamanızda ayrı yapmak yerine anahtarları yönetebilir ve tek bir yerde iptal edebilirsiniz.

## <a name="benefits-of-integration"></a>Tümleştirmenin avantajları

Microsoft Identity platformu ile tümleştirme, ek kod yazmanızı gerektirmeyen avantajlarla birlikte gelir.

### <a name="integration-with-enterprise-identity-management"></a>Kurumsal kimlik yönetimi ile tümleştirme

**Uygulamanızın BT ilkeleriyle uyumlu olması.**  Kuruluşlar kurumsal kimlik yönetimi sistemlerini Microsoft Identity platformu ile tümleştirdiğinden, bir kişi kuruluştan ayrıldığında, ek adımlara gerek duymadan otomatik olarak uygulamanıza erişimi kaybedecektir. Uygulamanıza kimlerin erişebileceğini yönetebilir ve hangi erişim ilkelerinin gerekli olduğunu belirleyebilirsiniz. Örneğin, çok faktörlü kimlik doğrulaması; karmaşık kurumsal ilkelerle uyum sağlamak için kod yazma gereksiniminizi azaltır. Azure AD, yöneticilere, kullanımı izleyebilmek için uygulamanızda oturum açan kişilerin ayrıntılı bir denetim günlüğünü sağlar.

**Uygulamanızın AD ile tümleştirebilmesi için Azure AD Active Directory buluta genişletir.**  Dünyanın dört bir yanındaki kuruluşlar, sorumlu oturum açma ve kimlik yönetimi sistemi olarak Active Directory kullanır ve uygulamalarının AD ile çalışmasını gerektirir. Azure AD ile tümleştirme, uygulamanızı Active Directory tümleştirir.

### <a name="advanced-security-features"></a>Gelişmiş güvenlik özellikleri

**Multi-Factor Authentication.**  Microsoft Identity platformu, yerel Multi-Factor Authentication sağlar. BT yöneticileri uygulamanıza erişmek için çok faktörlü kimlik doğrulamasının yapılmasını gerektirebilir. bu sayede, bu desteği kendiniz kodlamayın. [Multi-Factor Authentication](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)hakkında daha fazla bilgi edinin.

**Anormal oturum açma algılaması.**  Microsoft Identity platformu, şüpheli etkinlikleri algılamak ve BT yöneticilerine olası sorunları bildirmek için makine öğrenimi algoritmalarını kullanırken, günde bir milyardan fazla oturum açma işlemini işler. Microsoft Identity Platform oturum açma 'yı destekleyerek, uygulamanız bu korumanın avantajını alır. [Azure Active Directory Access raporunu görüntüleme](../reports-monitoring/overview-reports.md)hakkında daha fazla bilgi edinin.

**Koşullu erişim.**  Çok faktörlü kimlik doğrulamasına ek olarak Yöneticiler, kullanıcıların uygulamanızda oturum açmasını sağlamak için belirli koşulların karşılanmasını gerektirebilir. Ayarlanabilir koşullar, istemci cihazlarının IP adresi aralığını, belirtilen grupların üyeliğini ve erişim için kullanılan cihazın durumunu içerir. [Koşullu erişim Azure Active Directory](../conditional-access/overview.md)hakkında daha fazla bilgi edinin.

### <a name="easy-development"></a>Kolay geliştirme

**Endüstri standardı protokoller.**  Microsoft, sektör standartlarını desteklemeye kararlıdır. Microsoft Identity platformu, sektör standardı OAuth 2,0 ve OpenID Connect 1,0 protokollerini destekler. [Microsoft Identity platform kimlik doğrulama protokolleri](active-directory-v2-protocols.md)hakkında daha fazla bilgi edinin.

**Açık kaynak kitaplıkları.**  Microsoft, geliştirme sürecini hızlandırmak için popüler diller ve platformlar için tam olarak desteklenen açık kaynak kitaplıkları sağlar. Kaynak kodu Apache 2,0 kapsamında lisanslanır ve bunlara çatalınızda ve projelere geri katkıda bulunun. [Microsoft kimlik doğrulama kitaplığı (msal)](reference-v2-libraries.md)hakkında daha fazla bilgi edinin.

### <a name="worldwide-presence-and-high-availability"></a>Dünya çapındaki varlık ve yüksek kullanılabilirlik

**Azure AD, dünyanın dört bir yanındaki veri merkezlerinde dağıtılır ve saat içinde yönetilir ve izlenir.**  Azure AD, Microsoft Azure ve Microsoft 365 kimlik yönetimi sistemidir ve dünyanın dört bir yanındaki 28 veri merkezinde dağıtılır. Dizin verilerinin en az üç veri merkezine çoğaltılması garanti edilir. Küresel yük dengeleyiciler, kullanıcıların verilerini içeren en yakın Azure AD kopyasına erişmesini sağlar ve bir sorun algılanırsa istekleri otomatik olarak diğer veri merkezlerine yeniden yönlendirir.

## <a name="next-steps"></a>Sonraki adımlar

[Kod yazmaya başlayın](v2-overview.md#getting-started).

[Kullanıcıları Microsoft Identity platformunu kullanarak imzalama](./authentication-vs-authorization.md)