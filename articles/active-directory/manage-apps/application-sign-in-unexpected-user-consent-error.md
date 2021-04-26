---
title: Bir uygulamaya onay gerçekleştirilirken beklenmeyen hata oluştu | Microsoft Docs
description: Bir uygulamaya yönelik yarışmaya yönelik işlemler sırasında oluşabilecek ve bunlarla ilgili neler yapabileceğiniz hataları açıklar
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: iangithinji
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: ad216d0062928fc16b0f2226daabb6258d09063c
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378134"
---
# <a name="unexpected-error-when-performing-consent-to-an-application"></a>Uygulama için onay verirken beklenmedik bir hata

Bu makalede, bir uygulamaya yarışmaya yönelik işlem sırasında oluşabilecek hatalar ele alınmaktadır. Hata iletileri içermeyen beklenmedik onay istemlerinde sorun yaşıyorsanız, bkz. [Azure AD Için kimlik doğrulama senaryoları](../develop/authentication-vs-authorization.md).

Azure Active Directory ile tümleştirilen birçok uygulama, çalışması için diğer kaynaklara erişim izinleri gerektirir. Bu kaynaklar aynı zamanda Azure Active Directory ile tümleştirildiğinde, bunlara erişim izinleri genellikle ortak onay çerçevesi kullanılarak istenir. Genellikle uygulamanın ilk kez kullanıldığı, ancak uygulamanın sonraki kullanımıyla da gerçekleşebileceği bir onay istemi görüntülenir.

Bir kullanıcının bir uygulamanın gerektirdiği izinleri onaylaması için belirli koşulların doğru olması gerekir. Bu koşullar karşılanmazsa aşağıdaki hatalar meydana gelebilir.

## <a name="requesting-not-authorized-permissions-error"></a>Yetkilendirilmemiş izinleri isteme hatası
* **AADSTS90093:** &lt; clientAppDisplayName &gt; , izin vermek için yetkiniz olmayan bir veya daha fazla izin istiyor. Sizin adınıza bu uygulamayı kabul edebilen bir yöneticiye başvurun.
* **AADSTS90094:** &lt; clientAppDisplayName &gt; , kuruluşunuzda yalnızca bir yöneticinin verebileceği kaynaklara erişmek için izne sahip olmalıdır. Kullanabilmek için önce lütfen yöneticiden bu uygulamaya izin vermesini isteyin.

Bu hata, genel yönetici olmayan bir Kullanıcı yalnızca bir yöneticinin izin verebileceği izinleri isteyen bir uygulamayı kullanmayı denediğinde oluşur. Bu hata, kuruluş adına uygulamaya erişim izni veren bir yönetici tarafından çözülebilir.

Bu hata, Microsoft 'un izin isteğinin riskli olduğunu algılaması nedeniyle, bir kullanıcının bir uygulamaya katılmasının engellenmesinin önlenmesi durumunda da oluşabilir. Bu durumda, bir denetim olayı "ApplicationManagement" kategorisi, etkinlik türü "uygulamaya onay onayı" ve "riskli uygulama algılandı" durum nedeni ile de günlüğe kaydedilir.

Bu hatanın gerçekleşebileceği başka bir senaryo da uygulama için Kullanıcı atamasının gerekli olduğu, ancak yönetici onayı sağlanmadığında oluşur. Bu durumda, yöneticinin önce yönetici onayı sağlaması gerekir.   

## <a name="policy-prevents-granting-permissions-error"></a>İlke, izin verilmesini engelliyor
* **AADSTS90093:** Bir &lt; tenantdisplayname Yöneticisi, &gt; &lt; istenen izinleri uygulamanın adına vermemenizi önleyen bir ilke ayarladı &gt; . &lt; &gt; Bu uygulamaya sizin adınıza izin veren bir tenantdisplayname yöneticisiyle iletişim kurun.

Bu hata, bir genel yönetici kullanıcıların uygulamaları onaylamasına yönelik özelliği kapattığında oluşur, yönetici olmayan bir kullanıcı izin gerektiren bir uygulamayı kullanmayı dener. Bu hata, kuruluş adına uygulamaya erişim izni veren bir yönetici tarafından çözülebilir.

## <a name="intermittent-problem-error"></a>Aralıklı sorun hatası
* **AADSTS90090:** Oturum açma işlemi, clientappdisplayname 'e vermeye çalıştığınız izinleri kaydederek aralıklı bir sorunla karşılaştı gibi görünüyor &lt; &gt; . daha sonra yeniden deneyin.

Bu hata, aralıklı bir hizmet tarafı sorununun oluştuğunu gösterir. Uygulamayı yeniden doğrulamaya çalışırken çözülebilir.

## <a name="resource-not-available-error"></a>Kaynak kullanılamıyor hatası
* **AADSTS65005:** Uygulama, &lt; &gt; kullanılamayan bir kaynak resourceappdisplayname 'e erişmek için istenen izinleri istedi &lt; &gt; . 

Uygulama geliştiricisine başvurun.

##  <a name="resource-not-available-in-tenant-error"></a>Kaynak kiracı hatası içinde kullanılamıyor
* **AADSTS65005:** &lt; clientAppDisplayName &gt; , &lt; kuruluşunuzda kullanılan bir kaynak resourceappdisplayname 'e erişim istiyor &gt; &lt; &gt; . 

Bu kaynağın kullanılabilir olduğundan emin olun veya bir tenantdisplayname Yöneticisi ile iletişime geçin &lt; &gt; .

## <a name="permissions-mismatch-error"></a>İzin uyumsuzluğu hatası
* **AADSTS65005:** Uygulama, kaynak resourceappdisplayname 'e erişmek için izin istedi &lt; &gt; . Uygulama kaydı sırasında uygulamanın önceden nasıl yapılandırıldığına eşleşmediğinden bu istek başarısız oldu. Uygulama satıcısına başvurun. * *

Bu hatalar, bir Kullanıcı, bir kullanıcının, kuruluşun dizininde (kiracı) bulunamayan bir kaynak uygulamasına erişmek için izin isteme girişimi olduğunda oluşur. Bu durum çeşitli nedenlerden kaynaklanabilir:

-   İstemci uygulama geliştiricisi, uygulamasını yanlış olarak yapılandırmıştır ve bu da geçersiz bir kaynağa erişim istemesine neden olur. Bu durumda, uygulama geliştiricisi bu sorunu çözmek için istemci uygulamasının yapılandırmasını güncelleştirmelidir.

-   Hedef kaynak uygulamasını temsil eden bir hizmet sorumlusu kuruluşta yok veya geçmişte var, ancak kaldırıldı. Bu sorunu çözmek için, istemci uygulamasının bu uygulamaya izin isteyebilmesi için kaynak uygulamaya yönelik bir hizmet sorumlusu kuruluşta sağlanmalıdır. Hizmet sorumlusu, uygulamanın türüne bağlı olarak çeşitli yollarla sağlanabilir:

    -   Kaynak uygulama (Microsoft tarafından yayımlanan uygulamalar) için bir abonelik edinme

    -   Kaynak uygulamasına yarışmaya

    -   Uygulama izinlerini Azure portal aracılığıyla verme

    -   Azure AD uygulama galerisinden uygulamayı ekleme

## <a name="risky-app-error-and-warning"></a>Riskli uygulama hatası ve uyarısı
* **AADSTS900941:** Yönetici onayı gereklidir. Uygulama riskli olarak kabul edilir. (AdminConsentRequiredDueToRiskyApp)
* Bu uygulama riskli olabilir. Bu uygulamaya güveniyorsanız, lütfen yöneticinizden size erişim vermesini isteyin.
* **AADSTS900981:** Riskli bir uygulama için yönetici onayı isteği alındı. (AdminConsentRequestRiskyAppWarning)
* Bu uygulama riskli olabilir. Yalnızca bu uygulamaya güveniyorsanız devam edin.

Bu iletilerden her ikisi de, Microsoft izin isteğinin riskli olabileceğini tespit edildiğinde görüntülenir. Bir dizi diğer faktörde, bu durum [doğrulanmış bir yayımcının](../develop/publisher-verification-overview.md) uygulama kaydına eklenmemiş olması durumunda gerçekleşebilir. [Yönetici onayı iş akışı](configure-admin-consent-workflow.md) devre dışı bırakıldığında ilk hata kodu ve ileti son kullanıcılara gösterilir. Yönetici onayı iş akışı etkinleştirildiğinde ve yöneticiler için ikinci kod ve ileti son kullanıcılara gösterilir. 

Son kullanıcılar riskli olarak algılanan uygulamalara onay veremeyecektir. Yöneticiler erişebilir, ancak uygulamayı çok dikkatli bir şekilde değerlendirmeli ve dikkatli bir şekilde devam edebilir. Uygulama daha fazla inceleme sonrasında şüpheli görünüyorsa, onay ekranından Microsoft 'a bildirilebilir. 

## <a name="next-steps"></a>Sonraki adımlar 

[Azure Active Directory (v1 uç noktası) üzerinde uygulamalar, izinler ve onay](../develop/quickstart-register-app.md)<br>

[Azure Active Directory (v 2.0 uç noktası) kapsamlar, izinler ve onay](../develop/v2-permissions-and-consent.md)