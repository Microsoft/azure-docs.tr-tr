---
title: Koşullu erişim yalnızca rapor modu nedir? -Azure Active Directory
description: Koşullu erişim ilkesi dağıtımıyla ilgili olarak yalnızca rapor moduna yardım edebilir
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 05/01/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: c6f4e4851d406b14ed38665274401b96446ac6a0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104579085"
---
# <a name="what-is-conditional-access-report-only-mode"></a>Koşullu erişim yalnızca rapor modu nedir?

Koşullu erişim, doğru koşullarda doğru erişim denetimleri uygulanarak güvenli kalmak için müşterilerimiz tarafından yaygın olarak kullanılır. Ancak, kuruluşunuzda koşullu erişim ilkesini dağıtmaya yönelik güçlüklerden biri, son kullanıcılara etkisini belirlemesidir. Eski kimlik doğrulamasını engelleme, bir Kullanıcı popülasyonu için çok faktörlü kimlik doğrulaması gerektirme veya oturum açma risk ilkeleri uygulama gibi yaygın dağıtım girişimleriyle etkilenen kullanıcıların sayısını ve adlarını tahmin etmek zor olabilir. 

Yalnızca rapor modu, yöneticilerin koşullu erişim ilkelerinin ortamlarında etkinleştirilmeden önce etkisini değerlendirmesini sağlayan yeni bir koşullu erişim ilkesi durumudur.  Yalnızca rapor modunun yayınlanmasıyla:

- Koşullu erişim ilkeleri yalnızca rapor modunda etkinleştirilebilir.
- Oturum açma sırasında yalnızca rapor modundaki ilkeler değerlendirilir, ancak zorlanmaz.
- Sonuçlar, oturum açma günlüğü ayrıntılarının **koşullu erişim** ve **yalnızca rapor** sekmelerinde günlüğe kaydedilir.
- Azure Izleyici aboneliğine sahip müşteriler, koşullu erişim öngörüleri çalışma kitabını kullanarak koşullu erişim ilkelerinin etkilerini izleyebilir.

> [!VIDEO https://www.youtube.com/embed/NZbPYfhb5Kc]

> [!WARNING]
> Yalnızca rapor modundaki, uyumlu cihazlar gerektiren ilkeler, cihaz uyumluluğu zorlanmasa da, ilke değerlendirmesi sırasında bir cihaz sertifikası seçmesini, Mac, iOS ve Android 'de kullanıcılara istem verebilir. Bu istemler, cihaz uyumlu hale gelene kadar yinelenir. Son kullanıcıların oturum açma sırasında istemler almasını engellemek için cihaz platformları Mac, iOS ve Android cihaz uyumluluk denetimleri yapan yalnızca rapor ilkelerinden hariç tutun. Yalnızca rapor modunun "Kullanıcı eylemleri" kapsamına sahip koşullu erişim ilkeleri için geçerli olmadığına unutmayın.

![Azure AD oturum açma günlüğünde yalnızca rapor sekmesi](./media/concept-conditional-access-report-only/report-only-detail-in-sign-in-log.png)

## <a name="policy-results"></a>İlke sonuçları

Belirli bir oturum açma işlemi için yalnızca rapor modundaki bir ilke değerlendirildiğinde, dört yeni olası sonuç değeri vardır:

| Sonuç | Description |
| --- | --- |
| Yalnızca rapor: başarılı | Tüm yapılandırılmış ilke koşulları, gerekli etkileşimli olmayan izin denetimleri ve oturum denetimleri karşılandı. Örneğin, bir Multi-Factor Authentication gereksinimi, belirteçte zaten bulunan bir MFA talebi tarafından karşılanır veya uyumlu bir cihazda cihaz denetimi gerçekleştirerek uyumlu bir cihaz ilkesi karşılanır. |
| Yalnızca rapor: hata | Tüm yapılandırılmış ilke koşulları karşılandı, ancak gerekli etkileşimli olmayan izin denetimleri veya oturum denetimleri karşılanmadı. Örneğin, bir ilke bir blok denetiminin yapılandırıldığı bir kullanıcı için geçerlidir veya bir cihaz uyumlu bir cihaz ilkesiyle başarısız olur. |
| Yalnızca rapor: Kullanıcı eylemi gerekli | Tüm yapılandırılmış ilke koşulları karşılanmadı, ancak gerekli izin denetimlerini veya oturum denetimlerini karşılamak için Kullanıcı eylemi gerekli olacaktır. Yalnızca rapor modunda, kullanıcıdan gerekli denetimleri karşılaması istenmez. Örneğin, kullanıcılardan Multi-Factor Authentication sorunları veya kullanım koşulları istenmez.   |
| Yalnızca rapor: uygulanmadı | Yapılandırılmış tüm ilke koşulları karşılanmadı. Örneğin, Kullanıcı ilkeden çıkarılır veya ilke yalnızca belirli güvenilir adlandırılmış konumlar için geçerlidir. |

## <a name="conditional-access-insights-workbook"></a>Koşullu erişim öngörüleri çalışma kitabı

Yöneticiler yalnızca rapor modunda birden çok ilke oluşturma yeteneğine sahiptir, bu nedenle her bir ilkenin her iki etkisinin ve birlikte değerlendirilen birden çok ilkenin birleştirilmiş etkisinin anlaşılması gerekir. Yeni koşullu erişim öngörüleri çalışma kitabı, yöneticilerin koşullu erişim sorgularını görselleştirmesini ve belirli bir zaman aralığı, uygulama kümesi ve Kullanıcı için bir ilkenin etkisini izlemesini sağlar. 
 
## <a name="next-steps"></a>Sonraki adımlar

[Koşullu erişim ilkesinde yalnızca rapor modunu yapılandırma](howto-conditional-access-insights-reporting.md)
