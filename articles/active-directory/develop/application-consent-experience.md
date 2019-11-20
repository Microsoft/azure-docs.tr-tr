---
title: Azure AD uygulama onayı deneyimlerini anlama
titleSuffix: Microsoft identity platform
description: Azure AD 'de uygulamaları yönetirken ve geliştirirken nasıl kullanabileceğinizi öğrenmek için Azure AD onay deneyimleri hakkında daha fazla bilgi edinin
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: ryanwi
ms.reviewer: zachowd
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4356a0a26aa586f99766cc5166c17d301a9a194d
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72803910"
---
# <a name="understanding-azure-ad-application-consent-experiences"></a>Azure AD uygulama onayı deneyimlerini anlama

Azure Active Directory (Azure AD) uygulama onayı Kullanıcı deneyimi hakkında daha fazla bilgi edinin. Bu nedenle, kuruluşunuzun uygulamalarını akıllıca yönetebilir ve/veya daha sorunsuz bir izin deneyimiyle uygulama geliştirebilirsiniz.

## <a name="consent-and-permissions"></a>Onay ve izinler

Onay, bir kullanıcının adına korumalı kaynaklara erişmesi için yetkilendirme izni veren bir işlemdir. Bir yöneticiye veya kullanıcıya, kendi kuruluşlarına/bireysel verilerine erişim izni vermeniz istenebilir.

İzin verme için gerçek kullanıcı deneyimi, kullanıcının kiracısında ayarlanan ilkelere, kullanıcının yetki kapsamına (veya role) ve istemci uygulaması tarafından istenen [izin](https://docs.microsoft.com/azure/active-directory/develop/active-directory-permissions) türüne göre farklılık gösterir. Bu, uygulama geliştiricilerinin ve kiracı yöneticilerinin onay deneyimi üzerinde bir denetim sahibi olduğu anlamına gelir. Yöneticiler, kiracısındaki onay deneyimini denetlemek için bir kiracıya veya uygulamaya ilke ayarlama ve devre dışı bırakma esnekliğine sahiptir. Uygulama geliştiricileri ne tür izinlerin istenmekte olduğunu ve kullanıcılara Kullanıcı onay akışı veya yönetici onay akışı aracılığıyla rehberlik etmek istiyorlar.

- **Kullanıcı onay akışı** , bir uygulama geliştiricisi kullanıcıları yalnızca geçerli kullanıcı için izin kaydetme amacını taşıyan yetkilendirme uç noktasına yönlendirirse.
- **Yönetici onay akışı** , bir uygulama geliştiricisi kullanıcıları, kiracının tamamına izin kaydetme amacını taşıyan yönetici onay uç noktasına yönlendirirse. Yönetici onay akışının düzgün çalıştığından emin olmak için, uygulama geliştiricilerinin uygulama bildiriminde `RequiredResourceAccess` özelliğindeki tüm izinleri listebilmeleri gerekir. Daha fazla bilgi için bkz. [uygulama bildirimi](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest).

## <a name="building-blocks-of-the-consent-prompt"></a>Onay isteminin yapı taşları

Onay istemi, kullanıcıların, kendi adına korumalı kaynaklara erişmek için istemci uygulamasına güvenip güvenmediğine yönelik yeterli bilgilere sahip olmasını sağlamak üzere tasarlanmıştır. Derleme bloklarının anlaşılmasına izin veren kullanıcıların daha bilinçli kararlar almasına yardımcı olur ve geliştiricilerin daha iyi kullanıcı deneyimleri oluşturmasına yardımcı olur.

Aşağıdaki diyagram ve tablo, onay isteminin yapı taşları hakkında bilgi sağlar.

![Onay isteminin yapı taşları](./media/application-consent-experience/consent_prompt.png)

| # | Bileşen | Amaç |
| ----- | ----- | ----- |
| 1 | Kullanıcı tanımlayıcısı | Bu tanımlayıcı, istemci uygulamanın adına korumalı kaynaklara erişmek istediği kullanıcıyı temsil eder. |
| 2 | Başlık | Başlık, kullanıcıların kullanıcı veya yönetici onay akışından ilerleyecekseniz değişir. Kullanıcı izin akışı ' nda, yönetici izin akışında başlık "istenen Izinler" olacak şekilde başlıkta "kuruluşunuz için kabul et" adlı ek bir satır olacaktır. |
| 3 | Uygulama logosu | Bu görüntü, kullanıcıların bu uygulamanın erişmeye yönelik uygulama olup olmadığı konusunda görsel bir ipucu olmasını sağlar. Bu görüntü, uygulama geliştiricileri tarafından sağlanır ve bu görüntünün sahipliği doğrulanmaz. |
| 4 | Uygulama adı | Bu değer kullanıcılara hangi uygulamanın verilerine erişim istediğini bilgilendirmelidir. Bu ad, geliştiriciler tarafından sağlanır ve bu uygulama adının sahipliği doğrulanmaz. |
| 5 | Yayımcı etki alanı | Bu değer kullanıcılara, güvenilirliği değerlendirebilecekleri bir etki alanı sağlamalıdır. Bu etki alanı geliştiriciler tarafından sağlanır ve bu yayımcı etki alanının sahipliği onaylanır. |
| 6 | İzinler | Bu liste, istemci uygulaması tarafından istenen izinleri içerir. Kullanıcılar, kabul ettikleri her zaman, istemci uygulamanın hangi verileri adına erişim yetkisi olacağını anlamak için istenen izin türlerini her zaman değerlendirmelidir. Bir uygulama geliştiricisi olarak en iyi ayrıcalığa sahip izinlere en iyi şekilde erişim isteğinde bulunur. |
| 7 | İzin açıklaması | Bu değer, izinleri açığa çıkaran hizmet tarafından sağlanır. İzin açıklamalarını görmek için iznin yanındaki köşeli çift ayraca geçiş yapmanız gerekir. |
| 8 | Uygulama koşulları | Bu terimler, uygulamanın hizmet koşulları ve gizlilik bildirimi bağlantılarını içerir. Yayımcı, kurallarının hizmet koşullarına göre seviyelendirilmesini sağlamaktan sorumludur. Ayrıca, Yayımcı, kullandıkları ve Kullanıcı verilerini gizlilik bildirimiyle paylaşmaktan sorumludur. Yayımcı, çok kiracılı uygulamalar için bu değerlere bağlantı sağlamıyorsa, izin isteminde cıvaar bir uyarı olacaktır. |
| 9 | https://myapps.microsoft.com | Bu, kullanıcıların şu anda verilerine erişimi olan Microsoft dışı uygulamaları gözden geçirebileceği ve kaldırabildiği bağlantıdır. |

## <a name="common-consent-scenarios"></a>Ortak onay senaryoları

Bir kullanıcının ortak izin senaryolarında görebileceği onay deneyimleri aşağıda verilmiştir:

1. Bir uygulamaya erişen ve Kullanıcı onay akışına yönlendiren, yetkili kapsamında olan bir izin kümesi gerektiren kişiler.
    
    1. Yöneticiler, tüm kiracı adına izin veren geleneksel onay isteminde ek bir denetim görür. Denetim varsayılan olarak kapalı olacaktır, bu nedenle yalnızca Yöneticiler açıkça onay kutusu, tüm kiracı adına izin verileceğini kabul eder. Bugün itibariyle, bu onay kutusu yalnızca genel yönetici rolü için görünür, bu nedenle Cloud admin ve App admin bu onay kutusunu görmez.

        ![Senaryo 1a için onay istemi](./media/application-consent-experience/consent_prompt_1a.png)
    
    2. Kullanıcılar geleneksel onay istemi 'ni görür.

        ![Senaryo 1b için onay istemi](./media/application-consent-experience/consent_prompt_1b.png)

2. Bir uygulamaya erişen ve yetkilendirme kapsamı dışında en az bir izin gerektiren bireyler.
    1. Yöneticiler, 1 ile aynı uyarıyı görür. yukarıda gösterildim.
    2. Kullanıcıların uygulamaya onay vermesini engellenecektir ve bu kullanıcılara yöneticiden uygulamaya erişim istemesi bildirilir. 
                
        ![Senaryo 1b için onay istemi](./media/application-consent-experience/consent_prompt_2b.png)

3. Ya da yönetici onay akışına yönlendirilir.
    1. Yönetici kullanıcılar Yönetici onay istemi 'ni görür. Başlık ve izin açıklamaları Bu istem üzerinde değişti, değişiklikler bu istemi kabul eden olguyu vurgulaması, uygulamanın tüm kiracı adına istenen verilere erişim izni vermesini sağlar.
        
        ![Senaryo 1b için onay istemi](./media/application-consent-experience/consent_prompt_3a.png)
        
    1. Yönetici olmayan kullanıcılar yukarıda gösterilen 2. ii ile aynı ekranı görür.

## <a name="next-steps"></a>Sonraki adımlar
- [Azure AD onay çerçevesinin izin nasıl uyguladığı konusunda](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)adım adım bir genel bakış alın.
- Daha ayrıntılı bilgi edinmek için, çok kiracılı bir uygulamanın "Kullanıcı" ve "Yönetici" onayını uygulamak için [izin çerçevesini nasıl kullandığını](active-directory-devhowto-multi-tenant-overview.md) öğrenin ve daha gelişmiş çok katmanlı uygulama düzenlerini destekler.
- [Uygulamanın yayımcı etki alanını nasıl yapılandıracağınızı](howto-configure-publisher-domain.md)öğrenin.
