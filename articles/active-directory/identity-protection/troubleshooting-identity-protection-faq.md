---
title: Azure Active Directory 'de kimlik koruması için SSS
description: Sık sorulan sorular Azure AD Kimlik Koruması
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: troubleshooting
ms.date: 01/07/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6117b1ac78faf84d73f5a78202709aec7a1f84d9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99492595"
---
# <a name="frequently-asked-questions-identity-protection-in-azure-active-directory"></a>Azure Active Directory 'de sık sorulan sorular kimlik koruması

## <a name="dismiss-user-risk-known-issues"></a>Kullanıcı riski bilinen sorunlarını kapat

Klasik kimlik koruması ' nda **Kullanıcı riskini** kapatma, kullanıcının kimlik koruması olan risk GEÇMIŞINI **Azure AD**'ye ayarlar.

Kimlik koruması ' nda **Kullanıcı riskini kapat** kullanıcının kimlik koruması olan risk geçmişinde aktör olarak ayarlanır **\<Admin’s name with a hyperlink pointing to user’s blade\>** .

Kullanıcı riski geri ayırma akışında gecikmeye neden olan geçerli bir bilinen sorun var. "Kullanıcı risk ilkeniz" varsa, bu ilke "Kullanıcı riskini kapat" seçeneğine tıklanarak kapatılan kullanıcılara uygulamayı durdurur. Ancak, UX ile kapatılan kullanıcıların "risk durumunu" yenilemesinin bilinen gecikmeleri vardır. Geçici bir çözüm olarak, en son kullanıcıyı "risk durumu" görmek için tarayıcı düzeyindeki sayfayı yenileyin.


## <a name="frequently-asked-questions"></a>Sık sorulan sorular

### <a name="why-is-a-user-at-risk"></a>Kullanıcı neden risk altında?

Azure AD Kimlik Koruması müşteriyseniz, [riskli kullanıcılar](howto-identity-protection-investigate-risk.md#risky-users) görünümüne gidin ve risk altındaki kullanıcıya tıklayın. Alttaki çekmecede, ' risk geçmişi ' sekmesi, bir Kullanıcı riski değişikliğine yol açan tüm olayları gösterecektir. Kullanıcının tüm riskli oturum açma işlemlerini görmek için, ' kullanıcının riskli oturum açma işlemleri ' ne tıklayın. Bu kullanıcıya yönelik tüm risk algılamalarını görmek için, ' kullanıcının risk algılamaları ' seçeneğine tıklayın.

### <a name="why-was-my-sign-in-blocked-but-identity-protection-didnt-generate-a-risk-detection"></a>Oturum açma, neden engelleniyor, ancak kimlik koruması risk algılama işlemi oluşturmaz mi?
Oturum açma işlemleri, çeşitli nedenlerle engellenebilir. Kimlik koruması, kimlik doğrulama isteğinde doğru kimlik bilgileri kullanıldığında yalnızca risk algılamalarını ürettiğine dikkat edin. Bir Kullanıcı yanlış kimlik bilgilerini kullanıyorsa, kötü bir aktör doğru kimlik bilgilerini kullanmadığı takdirde kimlik bilgilerinin güvenliğinin olmaması nedeniyle kimlik koruması tarafından işaretlenmeyecektir. Bir kullanıcının bir kimlik koruması algılaması üretmeyecek bir şekilde imzalanmasına neden olabilecek bazı nedenler şunlardır:
* IP adresinden gelen kötü amaçlı etkinlik nedeniyle **IP engelleniyor** olabilir. IP engellenen ileti, kimlik bilgilerinin doğru olup olmadığını ayırt etmez. IP engellenirse ve kimlik bilgileri doğru değilse, Kimlik Koruması Algılama işlemi oluşturmaz
* **[Akıllı kilitleme](../authentication/howto-password-smart-lockout.md)** , birden çok başarısız girişimden sonra hesabın oturum açmasını engelleyebilir
* Bir kimlik doğrulama isteğini engellemek için risk düzeyinden farklı koşulları kullanan **koşullu erişim ilkesi** zorlanabilir

### <a name="how-can-i-get-a-report-of-detections-of-a-specific-type"></a>Belirli bir türdeki algılamalardan nasıl rapor alabilirim?

Risk algılamaları görünümüne gidin ve ' algılama türü 'ne göre filtreleme yapın. Daha sonra bu raporu ' de indirebilirsiniz. CSV veya. En üstteki **İndir** DÜĞMESINI kullanarak JSON biçimi. Daha fazla bilgi için bkz. [nasıl yapılır: riski araştırma](howto-identity-protection-investigate-risk.md#risk-detections).

### <a name="why-cant-i-set-my-own-risk-levels-for-each-risk-detection"></a>Her risk algılaması için kendi risk düzeylerimi neden ayarlayamıyorum?

Kimlik koruması 'ndaki risk düzeyleri algılama hassasiyetini temel alır ve denetimli makine öğrenimi tarafından desteklenir. Kullanıcıların hangi deneyimle sunulduğunu özelleştirmek için, Yönetici Kullanıcı riskidir Sign-In ve risk Ilkelerinden bazı kullanıcıları/grupları dahil edebilir/hariç tutabilir.

### <a name="why-does-the-location-of-a-sign-in-not-match-where-the-user-truly-signed-in-from"></a>Bir oturum açma konumu, kullanıcının gerçekten gerçekten oturum açmasından nasıl eşleşmez?

IP coğrafi coğrafi eşleme, sektör genelinde bir zorluk dır. Oturum açma raporunda listelenen konumun gerçek konumla eşleşmediğinden, Microsoft destek 'e ulaşın. 

### <a name="how-can-i-close-specific-risk-detections-like-i-did-in-the-old-ui"></a>Eski Kullanıcı arabiriminde yaptığımız gibi belirli risk algılamalarını nasıl kapatabilirim?

Bağlı oturum açmayı tehlikeye veya güvenli olarak onaylayarak risk algılamaları hakkında geri bildirimde bulunun. Oturum açma sırasında verilen geri bildirimler, bu oturum açma sırasında yapılan tüm algılamaları aşağı doğru ayırır. Bir oturum açma ile bağlantılı olmayan algılamaları kapatmak istiyorsanız, bu geri bildirimi Kullanıcı düzeyinde sağlayabilirsiniz. Daha fazla bilgi için bkz. [nasıl yapılır: risk geri bildirimi verme Azure AD kimlik koruması](howto-identity-protection-risk-feedback.md).

### <a name="how-far-can-i-go-back-in-time-to-understand-whats-going-on-with-my-user"></a>Kullanıcı ile neler olduğunu anlamak için zaman içinde ne kadar geri gidebilirim?

- [Riskli kullanıcılar](howto-identity-protection-investigate-risk.md#risky-users) görünümü, bir kullanıcının geçmişteki tüm oturum açma işlemlerini temel alarak risk altında olduğunu gösterir. 
- [Riskli oturum açma](howto-identity-protection-investigate-risk.md#risky-sign-ins) işlemleri görünümü son 30 gün içinde riskli işaretleri gösterir. 
- [Risk algılamaları](howto-identity-protection-investigate-risk.md#risk-detections) görünümü, son 90 gün içinde yapılan risk algılamalarını gösterir.

### <a name="how-can-i-learn-more-about-a-specific-detection"></a>Belirli bir algılama hakkında nasıl daha fazla bilgi edinebilirim?

Tüm risk algılamaları, [risk](concept-identity-protection-risks.md#risk-types-and-detection)altındaki makalede belgelenmiştir. Algılama hakkında daha fazla bilgi edinmek için Azure portal algılamanın yanındaki (i) simgenin üzerine gelin.

### <a name="how-do-the-feedback-mechanisms-in-identity-protection-work"></a>Kimlik koruması 'ndaki geri bildirim mekanizmaları nasıl çalışır?

**Tehlikede olduğunu onaylayın** (oturum açma sırasında) – oturum açma kimliğinin kimlik sahibi tarafından gerçekleştirilmediğini ve bir güvenliğinin aşıldığını belirtir Azure AD kimlik koruması bildirir.

- Bu geri bildirimin alınması sırasında, oturum açma ve Kullanıcı risk durumunu, **güvenliği aşılmış** ve risk düzeyini **yüksek** olarak geçeceğiz.

- Ayrıca, risk değerlendirmesinde gelecekteki geliştirmeler için Machine Learning sistemlerimize bilgi sağlıyoruz.

    > [!NOTE]
    > Kullanıcı zaten düzeltildiyse, oturum açma ve Kullanıcı risk durumunu **onaylanabilecek** ve risk düzeyi **yüksek** olarak taşıdığı için **güvenliği aşılmış** ' i tıklamayın.

**Güvenli** ' i (oturum açma sırasında) onayla – oturum açma 'nın kimlik sahibi tarafından gerçekleştirildiğini Azure AD kimlik koruması ve bir uzlaşmaya Işaret olmadığını bildirir.

- Bu geri bildirimin alınması sırasında, oturum açma (Kullanıcı değil) risk durumunu **onaylı güvenli** ve risk düzeyine taşıyacağız **-** .

- Ayrıca, risk değerlendirmesinde gelecekteki geliştirmeler için Machine Learning sistemlerimize bilgi sağlıyoruz. 

    > [!NOTE]
    >Bugün, oturum açma sırasında güvenli Onayla ' yı seçtiğinizde, aynı özelliklerle riskli olarak işaretlenmesinin ardından gelecek oturum açma işlemleri engellenir. Kullanıcının özelliklerini öğrenmek için sistemi eğmenin en iyi yolu, MFA ile riskli oturum açma ilkesini kullanmaktır. MFA için riskli oturum açma işlemleri yapıldığında ve Kullanıcı isteğe başarıyla yanıt verdiğinde, oturum açma işlemi başarılı olur ve sistemi meşru Kullanıcı davranışında eğitme konusunda yardımcı olur.
    >
    > Kullanıcının güvenliğinin aşılmadığını düşünüyorsanız, oturum açma düzeyinde **onaylanmış güvenli** kullanmak yerine Kullanıcı düzeyinde Kullanıcı **riskini kapat** seçeneğini kullanın. Kullanıcı düzeyindeki Kullanıcı **riskini** kapatma, Kullanıcı riskini ve geçmişteki tüm riskli oturum açma işlemlerini ve risk algılamalarını kapatır.

### <a name="why-am-i-seeing-a-user-with-a-low-or-above-risk-score-even-if-no-risky-sign-ins-or-risk-detections-are-shown-in-identity-protection"></a>Kimlik koruması 'nda riskli oturum açma veya risk algılamaları gösterilmese bile, bir kullanıcıyı düşük (veya üzeri) risk puanı ile neden görüyorum?

Kullanıcı riskini doğası gereği, kullanım süreleri dolana kadar, kimlik koruması sırasında son riskli oturum açma işlemleri veya risk algılamaları olmasa bile, bir kullanıcı düşük veya daha yüksek bir Kullanıcı riski oluşturabilir. Bu durum, bir kullanıcının riskli oturum açma işlemlerinin ayrıntılarını ve risk algılamalarını depolayabilmemiz için yalnızca kötü amaçlı etkinlik zaman dilimini aşacak şekilde gerçekleştiyse meydana gelebilir. Kötü aktörlerin, saldırılarına devam etmeden önce güvenliği aşılmış bir kimliğin arkasındaki 140 gün içinde müşterilerin ortamında kalması bilindiği için Kullanıcı riskini sona ermedik. Müşteriler kullanıcının risk zaman çizelgesini inceleyerek, bir kullanıcının neden risk altında olduğunu anlayabilir: `Azure Portal > Azure Active Directory > Risky users’ report > Click on an at-risk user > Details’ drawer > Risk history tab`

### <a name="why-does-a-sign-in-have-a-sign-in-risk-aggregate-score-of-high-when-the-detections-associated-with-it-are-of-low-or-medium-risk"></a>Neden bir oturum açma, bununla ilişkili algılamalar düşük veya orta riskli olduğunda bir "oturum açma riski (toplama)" puanı elde ediyor mu?

Yüksek toplu risk puanı, oturum açma işleminin diğer özelliklerine veya bu oturum açma için birden fazla algılamanın tetiklenmesi durumunda olabilir. Üstelik, oturum açma ile ilişkili algılamalar yüksek riskli olsa da, oturum açma, ortamın bir oturum açma riski (toplama) olabilir.

### <a name="what-is-the-difference-between-the-activity-from-anonymous-ip-address-and-anonymous-ip-address-detections"></a>"Anonim IP adresi etkinliği" ve "anonim IP adresi" algılamaları arasındaki fark nedir?

"Anonim IP adresi" algılaması kaynağı Azure AD Kimlik Koruması, "anonim IP adresinden gelen etkinlik" algılaması MCAS 'den (Microsoft Cloud App Security) tümleşiktir. Çok benzer adlara sahip olsa da, bu sinyallerin örtüşmesini görebileceğiniz için farklı arka uç algılamaları vardır.
