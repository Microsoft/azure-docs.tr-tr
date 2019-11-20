---
title: Azure AD parola koruması-Azure Active Directory
description: Azure AD parola korumasını kullanarak şirket içi Active Directory zayıf parolalar
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/18/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 013a14505f7ac1382bce369e161fdae834f605fc
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/23/2019
ms.locfileid: "71200221"
---
# <a name="enforce-azure-ad-password-protection-for-windows-server-active-directory"></a>Windows Server için Azure AD parola korumasını zorlama Active Directory

Azure AD parola koruması, bir kuruluşta parola ilkelerini geliştiren bir özelliktir. Parola korumasının şirket içi dağıtımı, Azure AD 'de depolanan hem genel hem de özel yasaklanmış parola listelerini kullanır. Azure AD ile bulut tabanlı değişiklikler için aynı denetimleri şirket içinde yapar. Bu denetimler parola değişiklikleri ve parola sıfırlama senaryolarında gerçekleştirilir.

## <a name="design-principles"></a>Tasarım ilkeleri

Azure AD parola koruması, bu ilkeler göz önünde bulundurularak tasarlanmıştır:

* Etki alanı denetleyicilerinin hiçbir şekilde doğrudan internet ile iletişim kurması gerekmez.
* Etki alanı denetleyicilerinde yeni ağ bağlantı noktaları açılmaz.
* Active Directory şeması değişikliği gerekli değildir. Yazılım, mevcut Active Directory **kapsayıcısını** ve **serviceConnectionPoint** şema nesnelerini kullanır.
* En az Active Directory etki alanı veya orman işlev düzeyi (DFL/FFL) gerekli değildir.
* Yazılım, koruduğu Active Directory etki alanlarında hesap oluşturmaz veya gerektirmez.
* Kullanıcı şifresiz-metin parolaları, parola doğrulama işlemleri sırasında veya herhangi bir zamanda etki alanı denetleyicisini hiçbir zaman bırakmaz.
* Yazılım diğer Azure AD özelliklerine bağımlı değildir; Örneğin, Azure AD parola karma eşitlemesi ilgili değildir ve Azure AD parola korumasının çalışması için gerekli değildir.
* Artımlı dağıtım desteklenir, ancak parola ilkesi yalnızca etki alanı denetleyicisi aracısının (DC Aracısı) yüklendiği yerde zorlanır. Daha ayrıntılı bilgi için sonraki konuya bakın.

## <a name="incremental-deployment"></a>Artımlı dağıtım

Azure AD parola koruması, Active Directory bir etki alanında etki alanı denetleyicileri arasında artımlı dağıtımı destekler ancak bunun ne anlama geldiğini ve bu avantajları anlamak önemlidir.

Azure AD parola koruması DC Aracısı yazılımı yalnızca bir etki alanı denetleyicisine yüklendiğinde ve yalnızca söz konusu etki alanı denetleyicisine gönderilen parola değişiklikleri için parolaları doğrulayabilir. Kullanıcı parolası değişikliklerini işlemek için Windows istemci makineleri tarafından hangi etki alanı denetleyicilerinin seçili olduğunu denetlemek mümkün değildir. Tutarlı davranış ve evrensel parola koruması güvenlik zorlamayı sağlamak için, DC Aracısı yazılımının bir etki alanındaki tüm etki alanı denetleyicilerine yüklenmesi gerekır.

Birçok kuruluş, tam bir dağıtım yapmadan önce etki alanı denetleyicilerinin bir alt kümesinde Azure AD parola korumasından dikkatli bir test yapmak istiyor. Azure AD parola koruması, kısmi dağıtımı destekler, bu, etki alanındaki diğer DC 'Ler DC Aracısı yazılımının yüklü olmadığı durumlarda bile, belirli bir DC 'deki DC Aracısı yazılımı parolaları etkin bir şekilde doğrulayacaktır. Bu türün kısmi dağıtımları güvenli DEĞILDIR ve test amaçları için dışında önerilmez.

## <a name="architectural-diagram"></a>Mimari diyagram

Azure AD parola korumasını şirket içi Active Directory ortamında dağıtmadan önce, temel alınan tasarım ve işlev kavramlarını anlamak önemlidir. Aşağıdaki diyagramda, parola koruma bileşenlerinin birlikte nasıl çalıştığı gösterilmektedir:

![Azure AD parola koruma bileşenlerinin birlikte çalışması](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

* Azure AD parola koruma proxy hizmeti, geçerli Active Directory ormanındaki etki alanına katılmış herhangi bir makinede çalışır. Birincil amacı, etki alanı denetleyicilerinden Azure AD 'ye parola ilkesi indirme isteklerini iletmektir. Daha sonra Azure AD 'den etki alanı denetleyicisine olan yanıtları döndürür.
* DC aracısının parola filtresi DLL 'SI, işletim sisteminden Kullanıcı parolası doğrulama isteklerini alır. Bu, etki alanı denetleyicisinde yerel olarak çalışan DC Aracısı Hizmetine iletir.
* Parola korumasının DC Aracısı hizmeti, DC aracısının parola filtresi DLL 'sinden parola doğrulama isteklerini alır. Geçerli (yerel olarak kullanılabilir) parola ilkesini kullanarak bunları işler ve sonucu döndürür: *geçti* veya *başarısız*.

## <a name="how-password-protection-works"></a>Parola koruması nasıl kullanılır?

Her Azure AD parola koruma proxy hizmeti örneği, Active Directory içinde bir **serviceConnectionPoint** nesnesi oluşturarak kendisini ormandaki etki alanı denetleyicilerine tanıtır.

Parola koruması için her DC Aracısı hizmeti ayrıca Active Directory bir **serviceConnectionPoint** nesnesi oluşturur. Bu nesne öncelikle raporlama ve Tanılama için kullanılır.

DC Aracısı hizmeti, Azure AD 'den yeni bir parola ilkesinin indirilmesini başlatmaktan sorumludur. İlk adım, proxy **serviceConnectionPoint** nesneleri için ormanı sorgulayarak BIR Azure AD parola koruma proxy hizmeti bulmaya yöneliktir. Kullanılabilir bir proxy hizmeti bulunduğunda, DC Aracısı proxy hizmetine bir parola ilkesi indirme isteği gönderir. İçindeki proxy hizmeti, isteği Azure AD 'ye gönderir. Proxy hizmeti daha sonra DC Aracısı hizmetine yanıtı döndürür.

DC Aracısı hizmeti, Azure AD 'den yeni bir parola ilkesi aldıktan sonra, bu ilkeyi etki alanı *SYSVOL* klasörü paylaşımının kökündeki ayrılmış bir klasörde depolar. DC Aracısı hizmeti, yeni ilkelerin ' de etki alanındaki diğer DC Aracısı hizmetlerinden çoğaltılmasına karşı bu klasörü de izler.

DC Aracısı hizmeti her zaman hizmet başlangıcında yeni bir ilke ister. DC Aracısı hizmeti başlatıldıktan sonra, geçerli yerel olarak kullanılabilir ilkenin yaşını saat olarak denetler. İlke bir saatten eskiyse, DC Aracısı daha önce açıklandığı gibi proxy hizmeti aracılığıyla Azure AD 'den yeni bir ilke ister. Geçerli ilke bir saatten eski değilse, DC Aracısı bu ilkeyi kullanmaya devam eder.

Bir Azure AD parola koruma parolası ilkesi indirildiğinde, bu ilke bir kiracıya özeldir. Diğer bir deyişle, parola ilkeleri her zaman Microsoft Global yasaklanmış-Password listesinin ve kiracı başına özel yasaklanmış-Password listesinin bir birleşimidir.

DC Aracısı, TCP üzerinden RPC aracılığıyla proxy hizmeti ile iletişim kurar. Proxy hizmeti, yapılandırmaya bağlı olarak, bu çağrıları dinamik veya statik bir RPC bağlantı noktasında dinler.

DC Aracısı, ağda kullanılabilir bir bağlantı noktasını hiçbir şekilde dinlemiyor.

Proxy hizmeti hiçbir şekilde DC Aracısı hizmetini çağırmayın.

Proxy hizmeti durum bilgisiz. İlkeleri veya Azure 'dan indirilen başka bir durumu hiçbir şekilde önbelleğe almaz.

DC Aracısı hizmeti, kullanıcının parolasını değerlendirmek için her zaman en son yerel olarak kullanılabilir parola ilkesini kullanır. Yerel DC 'de parola ilkesi yoksa, parola otomatik olarak kabul edilir. Bu durumda, yöneticinin uyarmak için bir olay iletisi günlüğe kaydedilir.

Azure AD parola koruması, gerçek zamanlı bir ilke uygulama altyapısı değildir. Azure AD 'de parola ilkesi yapılandırma değişikliği yapıldığında ve bu değişiklik tüm etki alanı denetleyicilerine ulaştığında ve uygulandığında bir gecikme olabilir.

Azure AD parola koruması, mevcut Active Directory parola ilkelerine ek olarak görev yapar. Bu, yüklenebilen diğer 3. taraf parola filtresi dll 'leri içerir. Active Directory her zaman, parolayı kabul etmeden önce tüm parola doğrulama bileşenlerinin kabul edilmesini gerektirir.

## <a name="foresttenant-binding-for-password-protection"></a>Parola koruması için orman/kiracı bağlama

Azure AD parola korumasının bir Active Directory ormanında dağıtılması için Azure AD ile ilgili ormanın kaydettirilmesi gerekir. Dağıtılan her ara sunucu hizmetinin da Azure AD 'ye kayıtlı olması gerekir. Bu orman ve proxy kayıtları, kayıt sırasında kullanılan kimlik bilgileri tarafından örtük olarak belirlenen belirli bir Azure AD kiracısı ile ilişkilendirilir.

Active Directory ormanı ve bir orman içindeki tüm dağıtılan proxy hizmetleri aynı kiracıya kaydedilmelidir. Bu ormanda farklı Azure AD kiracılarına Kaydolmakta olan bir Active Directory ormanı veya herhangi bir proxy hizmeti olması desteklenmez. Yanlış yapılandırılmış bir dağıtımın belirtileri, parola ilkelerini indirimeme ' yı içerir.

## <a name="download"></a>İndirme

Azure AD parola koruması için gereken iki aracı yükleyicisi, [Microsoft Indirme merkezi](https://www.microsoft.com/download/details.aspx?id=57071)' nden edinilebilir.

## <a name="next-steps"></a>Sonraki adımlar
[Azure AD parola korumasını dağıtma](howto-password-ban-bad-on-premises-deploy.md)
