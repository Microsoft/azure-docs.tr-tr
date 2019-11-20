---
title: Azure Active Directory ile tümleştirme | Microsoft Docs
description: Uygulamanızı Azure Active Directory ile tümleştirmeyle ilgili avantajları öğrenin ve Basitleştirilmiş oturum açma, kimlik yönetimi, çok faktörlü kimlik doğrulaması ve erişim denetimi gibi özelliklerle ilgili kaynakları alın.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: d13bba54-96bd-4b81-bee9-c8025ffa1648
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/08/2019
ms.author: ryanwi
ms.reviewer: bryanla
ms.custom: aaddev, seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: 48be16827da21643af485d1fb94b9a7b76728325
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72374044"
---
# <a name="integrating-with-azure-active-directory"></a>Azure Active Directory ile tümleştirme

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Bu makalede, uygulamanızı Azure Active Directory (Azure AD) ile tümleştirmenin avantajları ve tümleştirme için kaynaklar elde etmeniz hakkında bilgi edineceksiniz. Azure AD, kuruluşlara bulut uygulamaları için kurumsal düzeyde kimlik yönetimi sağlar. Azure AD tümleştirmesi kullanıcılarınıza kolay bir oturum açma deneyimi sağlar ve uygulamanızın BT ilkesine uymasını sağlar.

## <a name="how-to-integrate"></a>Tümleştirme

Uygulamanızın Azure AD ile tümleştirilmesine yönelik çeşitli yollar vardır. Uygulamanız için uygun olan bu senaryolardan çok sayıda veya daha az yararlanın.

### <a name="support-azure-ad-as-a-way-to-sign-in-to-your-application"></a>Azure AD 'yi uygulamanızda oturum açmak için bir yol olarak destekleme

**Oturum açma ve destek maliyetlerini azaltma.** Uygulamanızda oturum açmak için Azure AD 'yi kullanarak kullanıcılarınız hatırlayamayacak bir ad ve parolaya sahip olmayacaktır. Bir geliştirici olarak depolamak ve korumak için bir parola daha az olacaktır. Unutulmuş parola sıfırlamaları işlemek gerekmez, tek başına önemli bir tasarruf olabilir. Office 365 ve Microsoft Azure dahil olmak üzere dünyanın en popüler bulut uygulamaları için Azure AD güçleri oturum açın. Milyonlarca kuruluşun yüz milyonlarca kullanıcısı sayesinde, kullanıcılarınız zaten Azure AD 'de oturum açtı. [Azure AD oturum açma desteği ekleme](v1-authentication-scenarios.md)hakkında daha fazla bilgi edinin.

**Uygulamanıza kaydolmayı kolaylaştırın.**  Uygulamanıza kaydolma sırasında Azure AD, kayıt formunuzu önceden doldurabilmeniz veya tamamen ortadan kaldırabilmeniz için bir kullanıcı hakkında önemli bilgiler gönderebilir. Kullanıcılar, sosyal medya ve mobil uygulamalarda bulunanlara benzer tanıdık bir onay deneyimi aracılığıyla Azure AD hesabını kullanarak uygulamanıza kaydolabilir. Herhangi bir Kullanıcı, Azure AD ile tümleştirilmiş bir uygulamaya kaydolabilir ve BT katılımı gerekmeden oturum açabilir. [Azure AD hesabı oturum açma için uygulamanızı imzalama](../../app-service/configure-authentication-provider-aad.md)hakkında daha fazla bilgi edinin.

### <a name="browse-for-users-manage-user-provisioning-and-control-access-to-your-application"></a>Kullanıcılara gidin, Kullanıcı sağlamasını yönetin ve uygulamanıza erişimi denetleyin

**Dizindeki kullanıcılara gözatın.**  Kullanıcıların e-posta adresleri yazmalarını istemek yerine, kullanıcılar tarafından, kuruluştaki diğer kişileri aramasına ve bunlara gözatmasına yardımcı olmak için Graph API kullanın. Kullanıcılar, kuruluş hiyerarşisinin ayrıntılarını görüntüleme dahil olmak üzere tanıdık bir adres defteri stili arabirimi kullanarak gözatabilirler. [Graph API](active-directory-graph-api.md)hakkında daha fazla bilgi edinin.

**Müşterinizin zaten yönettiği Active Directory grupları ve dağıtım listelerini yeniden kullanın.**  Azure AD, müşterinizin e-posta dağıtımı ve erişim yönetimi için zaten kullandığı grupları içerir. Graph API kullanarak, müşterinizin uygulamanızda ayrı bir grup kümesi oluşturmasını ve yönetmesini istemek yerine bu grupları yeniden kullanın. Grup bilgileri de uygulamanıza oturum açma belirteçlerinde gönderilebilir. [Graph API](active-directory-graph-api.md)hakkında daha fazla bilgi edinin.

**Uygulamanıza kimlerin erişebileceğini denetlemek için Azure AD 'yi kullanın.**  Azure AD 'deki Yöneticiler ve uygulama sahipleri, belirli kullanıcılara ve gruplara uygulamalara erişim atayabilir. Graph API kullanarak, bu listeyi okuyabilir ve kaynak üzerinde kaynakları ve erişimi sağlamayı ve uygulama içinde erişimi denetlemek için kullanabilirsiniz.

**Rol tabanlı Access Control için Azure AD kullanın.**  Yöneticiler ve uygulama sahipleri, uygulamanızı Azure AD 'ye kaydettiğinizde tanımladığınız rollere kullanıcılar ve gruplar atayabilir. Rol bilgileri, uygulamanıza oturum açma belirteçlerinde gönderilir ve Graph API kullanarak da okunabilir. [Yetkilendirme Için Azure ad kullanma](https://cloudblogs.microsoft.com/enterprisemobility/2014/12/18/azure-active-directory-now-with-group-claims-and-application-roles/)hakkında daha fazla bilgi edinin.

### <a name="get-access-to-users-profile-calendar-email-contacts-files-and-more"></a>Kullanıcının profiline, takvimine, e-postasına, kişilerine, dosyalarına ve fazlasına erişim sağlayın

**Azure AD, Office 365 ve diğer Microsoft iş hizmetleri için yetkilendirme sunucusudur.**  Uygulamanızda oturum açmak için Azure AD 'yi destek, veya geçerli kullanıcı hesaplarınızı OAuth 2,0 kullanarak Azure AD Kullanıcı hesaplarına bağlamayı destekliyorsa, kullanıcının profiline, takvimine, e-postasına, kişilerine, dosyalarına ve diğer bilgilere okuma ve yazma erişimi isteyebilirsiniz. Kullanıcı takvimine olayları sorunsuzca yazabilir ve OneDrive 'a dosyaları okuyabilir veya yazabilirsiniz. [Office 365 API 'lerine erişme](https://msdn.microsoft.com/office/office365/howto/platform-development-overview)hakkında daha fazla bilgi edinin.

### <a name="promote-your-application-in-the-azure-and-office-365-marketplaces"></a>Uygulamanızı Azure ve Office 365 Market konumlarında yükseltin

**Uygulamanızı zaten Azure AD kullanan milyonlarca kuruluşa yükseltin.**  Bu markette arama yapan ve gözatabilen kullanıcılar zaten bir veya daha fazla bulut hizmeti kullanıyor ve bunları nitelikli bulut hizmeti müşterilerine yapıyor. [Azure Marketi](https://azure.microsoft.com/marketplace/partner-program/)'nde uygulamanızı yükseltme hakkında daha fazla bilgi edinin.

**Kullanıcılar uygulamanıza kaydolup Azure AD erişim panelinde ve Office 365 uygulama başlatıcısı 'nda görünür.**  Kullanıcılar daha sonra uygulamanıza hızlı ve kolay bir şekilde geri dönebilir ve kullanıcı etkileşimini geliştirir. [Azure AD erişim paneli](../user-help/active-directory-saas-access-panel-introduction.md)hakkında daha fazla bilgi edinin.

### <a name="secure-device-to-service-and-service-to-service-communication"></a>Cihazdan hizmete ve hizmetten hizmete iletişimi güvenli hale getirme

**Hizmet ve cihazların kimlik yönetimi için Azure AD 'nin kullanılması, yazmanız gereken kodu azaltır ve erişimi yönetmesini sağlar.**  Hizmet ve cihazlar, OAuth kullanarak Azure AD 'den belirteçler alabilir ve bu belirteçleri Web API 'Lerine erişmek için kullanabilir. Azure AD 'yi kullanarak karmaşık kimlik doğrulama kodu yazmadan kaçınabilirsiniz. Hizmet ve cihazların kimlikleri Azure AD 'de depolandığından, bunu uygulamanızda ayrı yapmak yerine anahtarları yönetebilir ve tek bir yerde iptal edebilirsiniz.

## <a name="benefits-of-integration"></a>Tümleştirmenin avantajları

Azure AD ile tümleştirme, ek kod yazmanızı gerektirmeyen avantajlarla birlikte gelir.

### <a name="integration-with-enterprise-identity-management"></a>Kurumsal kimlik yönetimi ile tümleştirme

**Uygulamanızın BT ilkeleriyle uyumlu olması.**  Kuruluşlar kurumsal kimlik yönetimi sistemlerini Azure AD ile tümleştirdiğinden, bir kişi kuruluştan ayrıldığında, ek adımlara gerek duymadan otomatik olarak uygulamanıza erişimi kaybedecektir. Uygulamanıza kimlerin erişebileceğini yönetebilir ve hangi erişim ilkelerinin gerekli olduğunu belirleyebilirsiniz. Örneğin, çok faktörlü kimlik doğrulaması; karmaşık kurumsal ilkelerle uyum sağlamak için kod yazma gereksiniminizi azaltır. Azure AD, yöneticilere, kullanımı izleyebilmek için uygulamanızda oturum açan kişilerin ayrıntılı bir denetim günlüğünü sağlar.

**Uygulamanızın AD ile tümleştirebilmesi için Azure AD Active Directory buluta genişletir.**  Dünyanın dört bir yanındaki kuruluşlar, sorumlu oturum açma ve kimlik yönetimi sistemi olarak Active Directory kullanır ve uygulamalarının AD ile çalışmasını gerektirir. Azure AD ile tümleştirme, uygulamanızı Active Directory tümleştirir.

### <a name="advanced-security-features"></a>İleri düzey güvenlik özellikleri

**Multi-Factor Authentication.**  Azure AD, yerel Multi-Factor Authentication sağlar. BT yöneticileri uygulamanıza erişmek için çok faktörlü kimlik doğrulamasının yapılmasını gerektirebilir. bu sayede, bu desteği kendiniz kodlamayın. [Multi-Factor Authentication](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)hakkında daha fazla bilgi edinin.

**Anormal oturum açma algılaması.**  Azure AD, şüpheli etkinlikleri algılamak ve BT yöneticilerine olası sorunları bildirmek için makine öğrenimi algoritmalarını kullanırken, günde bir milyardan daha fazla oturum açma işlemi. Azure AD oturum açma 'yı destekleyerek uygulamanız bu korumanın avantajını alır. [Azure Active Directory Access raporunu görüntüleme](../active-directory-view-access-usage-reports.md)hakkında daha fazla bilgi edinin.

**Koşullu erişim.**  Çok faktörlü kimlik doğrulamasına ek olarak Yöneticiler, kullanıcıların uygulamanızda oturum açmasını sağlamak için belirli koşulların karşılanmasını gerektirebilir. Ayarlanabilir koşullar, istemci cihazlarının IP adresi aralığını, belirtilen grupların üyeliğini ve erişim için kullanılan cihazın durumunu içerir. [Koşullu erişim Azure Active Directory](../active-directory-conditional-access-azure-portal.md)hakkında daha fazla bilgi edinin.

### <a name="easy-development"></a>Kolay geliştirme

**Endüstri standardı protokoller.**  Microsoft, sektör standartlarını desteklemeye kararlıdır. Microsoft Identity platformu, sektör standardı OAuth 2,0 ve OpenID Connect 1,0 protokollerini destekler. [Microsoft Identity platform kimlik doğrulama protokolleri](active-directory-v2-protocols.md)hakkında daha fazla bilgi edinin.

**Açık kaynak kitaplıkları.**  Microsoft, geliştirme sürecini hızlandırmak için popüler diller ve platformlar için tam olarak desteklenen açık kaynak kitaplıkları sağlar. Kaynak kodu Apache 2,0 kapsamında lisanslanır ve bunlara çatalınızda ve projelere geri katkıda bulunun. [Microsoft kimlik doğrulama kitaplığı (msal)](reference-v2-libraries.md)hakkında daha fazla bilgi edinin.

### <a name="worldwide-presence-and-high-availability"></a>Dünya çapındaki varlık ve yüksek kullanılabilirlik

**Azure AD, dünyanın dört bir yanındaki veri merkezlerinde dağıtılır ve saat içinde yönetilir ve izlenir.**  Azure AD, Microsoft Azure ve Office 365 kimlik yönetimi sistemidir ve dünyanın dört bir yanındaki 28 veri merkezinde dağıtılır. Dizin verilerinin en az üç veri merkezine çoğaltılması garanti edilir. Küresel yük dengeleyiciler, kullanıcıların verilerini içeren en yakın Azure AD kopyasına erişmesini sağlar ve bir sorun algılanırsa istekleri otomatik olarak diğer veri merkezlerine yeniden yönlendirir.

## <a name="next-steps"></a>Sonraki adımlar

[Kod yazmaya başlayın](v2-overview.md#getting-started).

[Kullanıcıları Microsoft Identity platformu kullanarak imzalama](v1-authentication-scenarios.md)

