---
title: Azure Active Directory daha esnek karma kimlik doğrulaması oluşturun
description: Mimarların ve BT yöneticilerinin dayanıklı bir karma altyapı oluşturma kılavuzu.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: de01788c01d4d6dedd9563faaaac07bff30bbd97
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98724768"
---
# <a name="build-resilience-in-your-hybrid-architecture"></a>Karma mimariniz üzerinde esnekliği oluşturma

Karma kimlik doğrulaması, kullanıcıların kimlikleri şirket içinde olan bulut tabanlı kaynaklara erişmesini sağlar. Karma altyapı hem bulut hem de şirket içi bileşenleri içerir.

* Bulut bileşenleri Azure AD, Azure kaynakları ve Hizmetleri, kuruluşunuzun bulut tabanlı uygulamaları ve SaaS uygulamaları içerir.

* Şirket içi bileşenler, şirket içi uygulamaları, SQL veritabanları gibi kaynakları ve Windows Server Active Directory gibi bir kimlik sağlayıcısını içerir. 

> [!IMPORTANT]
> Karma altyapınızda esnekliği planlaması yaparken, bağımlılıkları ve tek hata noktalarını en aza indirme anahtarıdır. 

Microsoft, karma kimlik doğrulaması için üç mekanizma sunmaktadır. Seçenekler esnekliği sırasına göre listelenmiştir. Mümkünse Parola karması eşitlemesini uygulamanızı öneririz.

* [Parola karması eşitleme](../hybrid/whatis-phs.md) (PHS), kimlik ve parolanın karma bir KARMASıNı Azure AD ile eşitlemek için Azure AD Connect kullanır ve kullanıcıların bulut tabanlı kaynaklara kendi parolalarıyla şirket içinde oturum açmasını sağlar. PHS, kimlik doğrulaması için değil yalnızca eşitleme için şirket içi bağımlılıklara sahiptir.

* [Geçişli kimlik doğrulaması](../hybrid/how-to-connect-pta.md) (PTA), kullanıcıların oturum açma IÇIN Azure AD 'ye yönlendirilmesini sağlar. Ardından, Kullanıcı adı ve parola, şirket ağında dağıtılan bir aracı aracılığıyla şirket içi Active Directory karşı onaylanır. PTA 'ın şirket içi sunucularda bulunan Azure AD PTA aracılarının şirket içi bir parmak izi vardır.

* [Federasyon](../hybrid/whatis-fed.md) müşterileri, AD FS gibi bir Federasyon Hizmeti dağıtır ve ardından Azure AD, Federasyon Hizmeti tarafından üretilen SAML onaylama 'yı doğrular. Federasyon, şirket içi altyapıda en yüksek bağımlılığa ve bu nedenle daha fazla hata noktasına sahiptir. 

   
Kuruluşunuzda bu yöntemlerden bir veya daha fazlasını kullanıyor olabilirsiniz. Daha fazla bilgi için bkz. [Azure AD karma kimlik çözümünüz için doğru kimlik doğrulama yöntemini seçme](../hybrid/choose-ad-authn.md). Bu makale, yönteminize karar vermenize yardımcı olabilecek bir karar ağacı içerir.

## <a name="password-hash-synchronization"></a>Parola karması eşitleme

Azure AD için en basit ve en dayanıklı karma kimlik doğrulama seçeneği, kimlik doğrulama isteklerini işlerken şirket içi kimlik altyapısı bağımlılığı olmayan [Parola karması eşitledir](../hybrid/whatis-phs.md) . Parola karmalarını içeren kimlikler Azure AD ile eşitlendiğinde, kullanıcılar şirket içi kimlik bileşenlerinde bağımlılık olmadan bulut kaynakları için kimlik doğrulaması yapabilir. 

![PHS mimari diyagramı](./media/resilience-in-hybrid/admin-resilience-password-hash-sync.png)

Bu kimlik doğrulama seçeneğini belirlerseniz, şirket içi kimlik bileşenleri kullanılamaz hale geldiğinde kesinti yaşanmaz. Donanım hatası, güç kesintileri, doğal felaketler ve kötü amaçlı yazılım saldırıları gibi birçok nedenden dolayı şirket içi kesinti meydana gelebilir. 

### <a name="how-do-i-implement-phs"></a>Nasıl yaparım? PHS 'yi uygulama

PHS 'yi uygulamak için aşağıdaki kaynaklara bakın:

* [Azure AD Connect ile parola karması eşitlemeyi uygulama](../hybrid/how-to-connect-password-hash-synchronization.md)

* [Parola karması eşitlemeyi etkinleştirme](../hybrid/how-to-connect-password-hash-synchronization.md)

Gereksinimleriniz, Fes 'yi kullanyükleyemezsiniz, geçişli kimlik doğrulaması kullanın.

## <a name="pass-through-authentication"></a>Doğrudan Kimlik Doğrulama

Geçişli kimlik doğrulaması, sunucularda şirket içinde bulunan kimlik doğrulama aracılarında bağımlılığa sahiptir. Kalıcı bir bağlantı veya hizmet veri yolu, Azure AD ile şirket içi PTA aracıları arasında bulunur. Güvenlik Duvarı, kimlik doğrulama aracılarını barındıran sunucular ve şirket içi Windows Server Active Directory (veya diğer kimlik sağlayıcısı) tüm olası hata noktalardır. 

![PTA 'ın mimari diyagramı](./media/resilience-in-hybrid/admin-resilience-pass-through-authentication.png)

### <a name="how-do-i-implement-pta"></a>PTA uygulamayı Nasıl yaparım? misiniz?

Doğrudan kimlik doğrulaması uygulamak için aşağıdaki kaynaklara bakın.

* [Geçişli kimlik doğrulamanın nasıl çalıştığı](../hybrid/how-to-connect-pta-how-it-works.md)

* [Geçişli kimlik doğrulaması güvenliğine derinlemesine bakış](../hybrid/how-to-connect-pta-security-deep-dive.md)

* [Azure AD geçişli kimlik doğrulamasını yükler](../hybrid/how-to-connect-pta-quick-start.md)

* PTA kullanıyorsanız, [yüksek oranda kullanılabilir bir topoloji](../hybrid/how-to-connect-pta-quick-start.md)tanımlayın.

 ## <a name="federation"></a>Federasyon

Federasyon, Azure AD ile, uç nokta değişimi, belirteç imzalama sertifikaları ve diğer meta veriler de dahil olmak üzere, Azure AD ile Federasyon Hizmeti arasında bir güven ilişkisi oluşturulmasını içerir. Azure AD 'ye bir istek geldiğinde, yapılandırmayı okur ve kullanıcıyı yapılandırılan uç noktalara yönlendirir. Bu noktada Kullanıcı, Azure AD tarafından doğrulanan bir SAML onayı veren Federasyon hizmetiyle etkileşime girer. 

Aşağıdaki diyagramda, birden çok şirket içi veri merkezinde gereksiz Federasyon ve Web uygulaması proxy sunucuları içeren bir kurumsal Active Directory Federasyon Hizmetleri (AD FS) (AD FS) dağıtımı gösterilmektedir. Bu yapılandırma DNS, coğrafi benzeşim özellikleri, güvenlik duvarları vb. ile Ağ Yük Dengelemesi gibi kuruluş ağ altyapısı bileşenlerini kullanır. Tüm şirket içi bileşenler ve bağlantılar hataya açıktır. Daha fazla bilgi için [AD FS kapasite planlama belgelerini](/windows-server/identity/ad-fs/design/planning-for-ad-fs-server-capacity) ziyaret edin.

> [!NOTE]
>  Federasyonun en yüksek sayıda şirket içi bağımlılığı ve bu nedenle en olası hata noktaları vardır. Bu diyagramda AD FS gösterildiği sırada, diğer şirket içi kimlik sağlayıcıları, yüksek kullanılabilirlik, ölçeklenebilirlik ve yük devretme işlemleri için benzer tasarım konularına tabidir.

![Federasyon mimarisi diyagramı](./media/resilience-in-hybrid/admin-resilience-federation.png)

 ### <a name="how-do-i-implement-federation"></a>Nasıl yaparım? Federasyonu mi uygulanacak?

Bir Federasyon kimlik doğrulama stratejisi uyguyor veya daha dayanıklı hale getirmek istiyorsanız, aşağıdaki kaynaklara bakın.

* [Federal kimlik doğrulaması nedir?](../hybrid/whatis-fed.md)

* [Federasyon nasıl çalışır?](../hybrid/how-to-connect-fed-whatis.md)

* [Azure AD federasyonu uyumluluk listesi](../hybrid/how-to-connect-fed-compatibility.md)

* [AD FS kapasite planlama belgelerini](/windows-server/identity/ad-fs/design/planning-for-ad-fs-server-capacity) izleyin

* [Azure IaaS 'de AD FS dağıtma](/windows-server/identity/ad-fs/deployment/how-to-connect-fed-azure-adfs)

* Federasyonla birlikte [PHS 'Yi etkinleştirin](../hybrid/tutorial-phs-backup.md)

## <a name="next-steps"></a>Sonraki adımlar
Yöneticiler ve Mimarlar için esnekliği kaynakları
 
* [Kimlik bilgisi yönetimiyle derleme esnekliği](resilience-in-credentials.md)

* [Cihaz durumlarıyla esnekliği derleme](resilience-with-device-states.md)

* [Sürekli erişim değerlendirmesi (CAE) kullanarak esnekliği oluşturma](resilience-with-continuous-access-evaluation.md)

* [Dış Kullanıcı kimlik doğrulamasında esnekliği derleme](resilience-b2b-authentication.md)

* [Uygulama proxy 'Si ile uygulama erişimi içinde derleme esnekliği](resilience-on-premises-access.md)

Geliştiriciler için esnekliği kaynakları

* [Uygulamalarınızda ıAM esnekliği oluşturma](resilience-app-development-overview.md)

* [CıHAR sistemlerinizde derleme esnekliği](resilience-b2c.md)