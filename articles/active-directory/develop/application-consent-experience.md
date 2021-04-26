---
title: Azure AD uygulama onayı deneyimleri
titleSuffix: Microsoft identity platform
description: Azure AD 'de uygulamaları yönetirken ve geliştirirken nasıl kullanabileceğinizi öğrenmek için Azure AD onay deneyimleri hakkında daha fazla bilgi edinin
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 04/06/2021
ms.author: ryanwi
ms.reviewer: jesakowi, asteen
ms.openlocfilehash: c570fc9f30d69f13546353cf6edab4122ae35142
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105467"
---
# <a name="understanding-azure-ad-application-consent-experiences"></a>Azure AD uygulama onayı deneyimlerini anlama

Azure Active Directory (Azure AD) uygulama onayı Kullanıcı deneyimi hakkında daha fazla bilgi edinin. Bu nedenle, kuruluşunuzun uygulamalarını akıllıca yönetebilir ve/veya daha sorunsuz bir izin deneyimiyle uygulama geliştirebilirsiniz.

## <a name="consent-and-permissions"></a>Onay ve izinler

Onay, bir kullanıcının adına korumalı kaynaklara erişmesi için yetkilendirme izni veren bir işlemdir. Bir yöneticiye veya kullanıcıya, kendi kuruluşlarına/bireysel verilerine erişim izni vermeniz istenebilir.

İzin verme için gerçek kullanıcı deneyimi, kullanıcının kiracısında ayarlanan ilkelere, kullanıcının yetki kapsamına (veya role) ve istemci uygulaması tarafından istenen [izin](v2-permissions-and-consent.md) türüne göre farklılık gösterir. Bu, uygulama geliştiricilerinin ve kiracı yöneticilerinin onay deneyimi üzerinde bir denetim sahibi olduğu anlamına gelir. Yöneticiler, kiracısındaki onay deneyimini denetlemek için bir kiracıya veya uygulamaya ilke ayarlama ve devre dışı bırakma esnekliğine sahiptir. Uygulama geliştiricileri ne tür izinlerin istenmekte olduğunu ve kullanıcılara Kullanıcı onay akışı veya yönetici onay akışı aracılığıyla rehberlik etmek istiyorlar.

- **Kullanıcı onay akışı** , bir uygulama geliştiricisi kullanıcıları yalnızca geçerli kullanıcı için izin kaydetme amacını taşıyan yetkilendirme uç noktasına yönlendirirse.
- **Yönetici onay akışı** , bir uygulama geliştiricisi kullanıcıları, kiracının tamamına izin kaydetme amacını taşıyan yönetici onay uç noktasına yönlendirirse. Yönetici onay akışının düzgün çalıştığından emin olmak için uygulama geliştiricilerinin, uygulama bildirimindeki özelliğindeki tüm izinleri listebilmeleri gerekir `RequiredResourceAccess` . Daha fazla bilgi için bkz. [uygulama bildirimi](./reference-app-manifest.md).

## <a name="building-blocks-of-the-consent-prompt"></a>Onay isteminin yapı taşları

Onay istemi, kullanıcıların, kendi adına korumalı kaynaklara erişmek için istemci uygulamasına güvenip güvenmediğine yönelik yeterli bilgilere sahip olmasını sağlamak üzere tasarlanmıştır. Derleme bloklarının anlaşılmasına izin veren kullanıcıların daha bilinçli kararlar almasına yardımcı olur ve geliştiricilerin daha iyi kullanıcı deneyimleri oluşturmasına yardımcı olur.

Aşağıdaki diyagram ve tablo, onay isteminin yapı taşları hakkında bilgi sağlar.

:::image type="content" source="./media/application-consent-experience/consent_prompt.png" alt-text="Onay isteminin yapı taşları":::

| # | Bileşen | Amaç |
| ----- | ----- | ----- |
| 1 | Kullanıcı tanımlayıcısı | Bu tanımlayıcı, istemci uygulamanın adına korumalı kaynaklara erişmek istediği kullanıcıyı temsil eder. |
| 2 | Başlık | Başlık, kullanıcıların kullanıcı veya yönetici onay akışından ilerleyecekseniz değişir. Kullanıcı izin akışı ' nda, yönetici izin akışında başlık "istenen Izinler" olacak şekilde başlıkta "kuruluşunuz için kabul et" adlı ek bir satır olacaktır. |
| 3 | Uygulama logosu | Bu görüntü, kullanıcıların bu uygulamanın erişmeye yönelik uygulama olup olmadığı konusunda görsel bir ipucu olmasını sağlar. Bu görüntü, uygulama geliştiricileri tarafından sağlanır ve bu görüntünün sahipliği doğrulanmaz. |
| 4 | Uygulama adı | Bu değer kullanıcılara hangi uygulamanın verilerine erişim istediğini bilgilendirmelidir. Bu ad, geliştiriciler tarafından sağlanır ve bu uygulama adının sahipliği doğrulanmaz. |
| 5 | Yayımcı etki alanı | Bu değer kullanıcılara, güvenilirliği değerlendirebilecekleri bir etki alanı sağlamalıdır. Bu etki alanı geliştiriciler tarafından sağlanır ve bu yayımcı etki alanının sahipliği onaylanır. |
| 6 | Yayımcı doğrulandı | Mavi "doğrulanan" rozet, uygulama yayımcısının kimlik Microsoft İş Ortağı Ağı bir hesap kullanarak kimliğini doğruladığından ve doğrulama işlemini tamamladığı anlamına gelir.|
| 7 | Yayımcı bilgileri  | Uygulamanın Microsoft veya kuruluşunuz tarafından yayınlanıp yayınlanmadığını görüntüler. |
| 8 | İzinler | Bu liste, istemci uygulaması tarafından istenen izinleri içerir. Kullanıcılar, kabul ettikleri her zaman, istemci uygulamanın hangi verileri adına erişim yetkisi olacağını anlamak için istenen izin türlerini her zaman değerlendirmelidir. Bir uygulama geliştiricisi olarak en iyi ayrıcalığa sahip izinlere en iyi şekilde erişim isteğinde bulunur. |
| 9 | İzin açıklaması | Bu değer, izinleri açığa çıkaran hizmet tarafından sağlanır. İzin açıklamalarını görmek için iznin yanındaki köşeli çift ayraca geçiş yapmanız gerekir. |
| 10| Uygulama koşulları | Bu terimler, uygulamanın hizmet koşulları ve gizlilik bildirimi bağlantılarını içerir. Yayımcı, kurallarının hizmet koşullarına göre seviyelendirilmesini sağlamaktan sorumludur. Ayrıca, Yayımcı, kullandıkları ve Kullanıcı verilerini gizlilik bildirimiyle paylaşmaktan sorumludur. Yayımcı, çok kiracılı uygulamalar için bu değerlere bağlantı sağlamıyorsa, izin isteminde cıvaar bir uyarı olacaktır. |
| 11 | https://myapps.microsoft.com | Bu, kullanıcıların şu anda verilerine erişimi olan Microsoft dışı uygulamaları gözden geçirebileceği ve kaldırabildiği bağlantıdır. |
| 12 | Buraya bildir | Bu bağlantı, uygulamaya güvenmediğiniz durumlarda şüpheli bir uygulamayı raporlamak için kullanılır. uygulamanın, verilerinizi kötüye kullanmasının veya başka bir nedenden dolayı bu uygulamayı taklit edecek olduğunu düşünüyorsanız. |

## <a name="app-requires-a-permission-within-the-users-scope-of-authority"></a>Uygulama, kullanıcının yetki kapsamında bir izin gerektirir

Ortak bir onay senaryosu, kullanıcının, kullanıcının yetki kapsamındaki bir izin kümesi gerektiren bir uygulamaya eriştiği anlamına gelir. Kullanıcı, Kullanıcı onay akışına yönlendirilir.

Yöneticiler, tüm kiracı adına izin veren geleneksel onay isteminde ek bir denetim görür. Denetim varsayılan olarak kapalı olacaktır, bu nedenle yalnızca Yöneticiler açıkça onay kutusu, tüm kiracı adına izin verileceğini kabul eder. Bugün itibariyle, bu onay kutusu yalnızca genel yönetici rolü için görünür, bu nedenle Cloud admin ve App admin bu onay kutusunu görmez.

:::image type="content" source="./media/application-consent-experience/consent_prompt_1a.png" alt-text="Senaryo 1a için onay istemi":::

Kullanıcılar geleneksel onay istemi 'ni görür.

:::image type="content" source="./media/application-consent-experience/consent_prompt_1b.png" alt-text="Geleneksel onay istemi 'ni gösteren ekran görüntüsü.":::

## <a name="app-requires-a-permission-outside-of-the-users-scope-of-authority"></a>Uygulama, kullanıcının yetki kapsamı dışında bir izin gerektiriyor

Diğer bir yaygın onay senaryosu, kullanıcının, kullanıcının yetki kapsamı dışında en az bir izin gerektiren bir uygulamaya eriştiği anlamına gelir.

Yöneticiler, tüm kiracı adına izin veren geleneksel onay isteminde ek bir denetim görür.

:::image type="content" source="./media/application-consent-experience/consent_prompt_1a.png" alt-text="Senaryo 1a için onay istemi":::

Yönetici olmayan kullanıcıların uygulamaya onay vermemesini engellenecektir ve yöneticiden uygulamaya erişim izni vermesini söylenecektir.

:::image type="content" source="./media/application-consent-experience/consent_prompt_2b.png" alt-text="Kullanıcıya uygulamaya erişmek için bir yönetici sormasını söyleyen onay isteminin ekran görüntüsü.":::

## <a name="user-is-directed-to-the-admin-consent-flow"></a>Kullanıcı yönetici onay akışına yönlendirilir

Diğer bir yaygın senaryo, kullanıcının yönetici onay akışına ne zaman gittiği veya bu akışa yönlendirilme durumunda olur.

Yönetici kullanıcılar Yönetici onay istemi 'ni görür. Başlık ve izin açıklamaları Bu istem üzerinde değişti, değişiklikler bu istemi kabul eden olguyu vurgulaması, uygulamanın tüm kiracı adına istenen verilere erişim izni vermesini sağlar.

:::image type="content" source="./media/application-consent-experience/consent_prompt_3a.png" alt-text="Senaryo 3A için onay istemi":::

Yönetici olmayan kullanıcıların uygulamaya onay vermemesini engellenecektir ve yöneticiden uygulamaya erişim izni vermesini söylenecektir.

:::image type="content" source="./media/application-consent-experience/consent_prompt_2b.png" alt-text="Kullanıcıya uygulamaya erişmek için bir yönetici sormasını söyleyen onay isteminin ekran görüntüsü.":::

## <a name="next-steps"></a>Sonraki adımlar

- [Azure AD onay çerçevesinin izin nasıl uyguladığı konusunda](./quickstart-register-app.md)adım adım bir genel bakış alın.
- Daha ayrıntılı bilgi edinmek için, çok kiracılı bir uygulamanın "Kullanıcı" ve "Yönetici" onayını uygulamak için [izin çerçevesini nasıl kullandığını](./howto-convert-app-to-be-multi-tenant.md) öğrenin ve daha gelişmiş çok katmanlı uygulama düzenlerini destekler.
- [Uygulamanın yayımcı etki alanını nasıl yapılandıracağınızı](howto-configure-publisher-domain.md)öğrenin.