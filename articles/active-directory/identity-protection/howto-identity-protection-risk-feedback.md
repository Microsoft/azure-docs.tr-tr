---
title: Azure AD Kimlik Koruması risk algılamaları hakkında geri bildirim sağlayın Azure Active Directory
description: Kimlik koruması risk algılamaları hakkında nasıl ve neden geri bildirim sağlamanız gerekir.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: be83f7fd71a4e5b300bd2a04bf335b494d9f05ac
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72887474"
---
# <a name="how-to-give-risk-feedback-in-azure-ad-identity-protection"></a>Nasıl yapılır: Azure AD Kimlik Koruması risk geri bildirimi verme

Azure AD Kimlik Koruması risk değerlendirmesi hakkında geri bildirimde bulunmak için izin verir. Aşağıdaki belge, Azure AD Kimlik Koruması risk değerlendirmesi ve nasıl dahil ettiğimiz hakkında geri bildirim vermek istediğiniz senaryoları listeler.

## <a name="what-is-a-detection"></a>Algılama nedir?

Kimlik Koruması Algılama, bir kimlik riski perspektifinden şüpheli etkinliğin göstergesidir. Bu şüpheli etkinliklere risk algılamaları denir. Bu kimlik tabanlı algılamalar buluşsal yöntemler, makine öğrenimi veya iş ortağı ürünlerinden gelebilir. Bu algılamalar, oturum açma riskini ve Kullanıcı riskini belirlemede kullanılır,

* Kullanıcı riski, bir kimliğin güvenliğinin aşıldığına ilişkin olasılığı temsil eder.
* Oturum açma riski, bir oturum açma olasılığının güvenliğinin aşıldığını temsil eder (örneğin, oturum açma kimlik sahibi tarafından yetkilendirilmemiş).

## <a name="why-should-i-give-risk-feedback-to-azure-ads-risk-assessments"></a>Azure AD 'nin risk değerlendirmelerine neden risk geri bildirimi vermem gerekir? 

Azure AD risk geri bildirimi sağlamanız gereken birkaç neden vardır:

- **Azure AD 'nin Kullanıcı veya oturum açma risk değerlendirmesi yanlış**. Örneğin, ' riskli oturum açma ' raporunda gösterilen bir oturum açma işlemi zararsız ve bu oturum açma üzerindeki tüm algılamalar hatalı pozitif sonuç.
- **Azure AD 'nin Kullanıcı veya oturum açma risk değerlendirmesi doğru olduğunu doğruladı**. Örneğin, ' riskli oturum açma ' raporunda gösterilen bir oturum, gerçekten kötü amaçlı ve Azure AD 'nin oturum açma ile ilgili tüm algılamaları doğru pozitif sonuçlar olduğunu bilmesini istiyorsunuz.
- **Bu Kullanıcı üzerindeki riski Azure AD kimlik koruması dışında düzeltildi** ve kullanıcının risk düzeyinin güncelleştirilmesini istiyorsunuz.

## <a name="how-does-azure-ad-use-my-risk-feedback"></a>Azure AD risk geri bildirimimi nasıl kullanır?

Azure AD, temel alınan Kullanıcı ve/veya oturum açma riskini ve bu olayların doğruluğunu güncelleştirmek için geri bildirimlerinizi kullanır. Bu geri bildirim, son kullanıcının güvenliğinin sağlanmasına yardımcı olur. Örneğin, bir oturum açma işlemi tehlikeye atıldıktan sonra Azure AD, kullanıcının riskini ve oturum açma riskini (gerçek zamanlı risk değil) yüksek olarak arttırır. Bu Kullanıcı, yüksek riskli kullanıcıların parolalarını güvenli bir şekilde sıfırlamasına zorlamak için Kullanıcı risk ilkenize dahil ediliyorsa, Kullanıcı bir sonraki oturum açtıklarında kendiliğinden otomatik olarak düzeltecektir.

## <a name="how-should-i-give-risk-feedback-and-what-happens-under-the-hood"></a>Risk geri bildirimi ve bu sırada ne olur?

Azure AD 'ye risk geri bildirimi sağlamak için senaryolar ve mekanizmalar aşağıda verilmiştir.

| Senaryo | Geri bildirimde bulunun | Devlet kapsamında ne olur? | Notlar |
| --- | --- | --- | --- |
| **Oturum açma güvenliği aşılmış (yanlış pozitif)** <br> ' Riskli oturum açma ' raporu, risk altındaki oturum açma işlemlerini (risk durumu = riskli) gösterir ancak bu oturum açma işlemi tehlikeye atılmadı. | Oturum açma ' yı seçin ve ' oturumu güvenli Onayla ' seçeneğine tıklayın. | Azure AD, oturum açma 'nın toplu riskini None olarak taşıyacaktır [risk durumu = onaylanmış güvenli; Risk düzeyi (toplama) =-] ve Kullanıcı riski üzerindeki etkisini tersine çevirir. | Şu anda ' oturumu güvenli Onayla ' seçeneği yalnızca ' riskli oturum açma ' raporunda kullanılabilir. |
| **Oturum açma güvenliği aşılmış (gerçek pozitif)** <br> ' Riskli oturum açma ' raporu, düşük riskli [risk düzeyi (toplama) = düşük] sahip olan ve oturum açma işlemi tehlikeye aşılmıştır. | Oturum açma ' yı seçin ve ' bir oturum açmayı onayla ' ya tıklayın. | Azure AD, oturum açma 'nın toplu riskini ve Kullanıcı riskini yüksek [risk durumu = Onaylandı olarak ele alacak şekilde taşıyacaktır. Risk düzeyi = yüksek]. | Şu anda ' yeniden oturum açmayı onayla ' seçeneği yalnızca ' riskli oturum açma ' raporunda kullanılabiliyor. |
| **Kullanıcı güvenliği aşılmış (gerçek pozitif)** <br> ' Riskli kullanıcılar ' raporu, düşük riskli [risk düzeyi = düşük] olan ve bu kullanıcının güvenliği ihlal edilmiş bir risk (risk durumu = riskli) gösterir. | Kullanıcıyı seçin ve ' kullanıcıyı güvenliği aşılmış ' seçeneğine tıklayın. | Azure AD, Kullanıcı riskini yüksek [riskli durum = Onaylandı olarak ele alacak şekilde taşıyacaktır. Risk düzeyi = yüksek] ve yeni bir algılama ' yönetici tarafından onaylanan Kullanıcı güvenliği aşılmış ' ekler. | Şu anda ' Kullanıcı güvenliği aşılmış ' seçeneği yalnızca ' riskli kullanıcılar ' raporunda kullanılabilir. <br> ' Riskli kullanıcılar ' raporundaki ' bir oturum açma ile bağlantılı olmayan risk algılamaları ' sekmesinde ' yönetici tarafından onaylanan Kullanıcı güvenliği aşılmış ' ' i algılama işlemi gösteriliyor. |
| **Kullanıcı Azure AD Kimlik Koruması dışında düzeltildi (doğru pozitif + düzeltilen)** <br> ' Riskli kullanıcılar ' raporu, risk altındaki kullanıcıyı gösterir ve daha sonra kullanıcıyı Azure AD Kimlik Koruması dışında düzeltildi. | 1. Kullanıcı seçin ve ' kullanıcıyı güvenliği aşılmış ' seçeneğine tıklayın. (Bu işlem, kullanıcının gerçekten tehlikeye girdiği Azure AD 'ye onaylar.) <br> 2. kullanıcının ' risk düzeyi ' öğesinin yüksek 'e gitmesini bekleyin. (Bu kez, Azure AD 'ye, risk motoruna yukarıdaki geri bildirimin uygulanması için gereken süre verilir.) <br> 3. kullanıcıyı seçin ve ' Kullanıcı riskini Kapat ' seçeneğine tıklayın. (Bu işlem, Azure AD 'ye kullanıcının artık güvenliği aşılmış olduğunu onaylar.) |  Azure AD, Kullanıcı riskini yok 'a taşır [risk durumu = kapatıldı; Risk düzeyi =-] ve etkin risk sahibi olan tüm mevcut oturum açma işlemlerinin riskini kapatır. | ' Kullanıcı riskini Kapat ' tıklandığında, Kullanıcı ve oturum açma işlemlerinin tüm riskleri kapatılır. Bu eylem geri alınamaz. |
| **Kullanıcı tehlikeye düşmedi (yanlış pozitif)** <br> ' Riskli kullanıcılar ' raporu risk altında Kullanıcı tarafından gösteriliyor, ancak kullanıcının güvenliği aşılmış değil. | Kullanıcıyı seçin ve ' Kullanıcı riskini Kapat ' seçeneğine tıklayın. (Bu işlem, Azure AD 'ye kullanıcının güvenliğinin aşılmadığı doğrular.) | Azure AD, Kullanıcı riskini yok 'a taşır [risk durumu = kapatıldı; Risk düzeyi =-]. | ' Kullanıcı riskini Kapat ' tıklandığında, Kullanıcı ve oturum açma işlemlerinin tüm riskleri kapatılır. Bu eylem geri alınamaz. |
| Kullanıcı riskini kapatmak istiyorum, ancak kullanıcının tehlikeye düşmesi/güvende olup olmadığından emin değilim. | Kullanıcıyı seçin ve ' Kullanıcı riskini Kapat ' seçeneğine tıklayın. (Bu işlem, Azure AD 'ye kullanıcının artık güvenliği aşılmış olduğunu onaylar.) | Azure AD, Kullanıcı riskini yok 'a taşır [risk durumu = kapatıldı; Risk düzeyi =-]. | ' Kullanıcı riskini Kapat ' tıklandığında, Kullanıcı ve oturum açma işlemlerinin tüm riskleri kapatılır. Bu eylem geri alınamaz. ' Parolayı Sıfırla ' ya tıklayarak kullanıcıyı düzeltmeniz veya Kullanıcı kimlik bilgilerini güvenli bir şekilde sıfırlamasını/değiştirmesini istemeniz önerilir. |

Kimlik koruması 'ndaki Kullanıcı risk algılamalarının geri bildirimi çevrimdışı olarak işlenir ve güncelleştirme biraz zaman alabilir. Risk işleme durumu sütunu, geri bildirim işlemenin geçerli durumunu sağlar.

![Riskli Kullanıcı raporu için risk işleme durumu](./media/howto-identity-protection-risk-feedback/risky-users-provide-feedback.png)

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Active Directory Kimlik Koruması risk algılamaları başvurusu](risk-events-reference.md)
