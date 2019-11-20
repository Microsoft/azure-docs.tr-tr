---
title: Azure Active Directory 'de kimlik koruması için SSS
description: Sık sorulan sorular Azure AD Kimlik Koruması
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: troubleshooting
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d22973867782ddb64ced2ac95e84c0b27a3addd
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72887604"
---
# <a name="frequently-asked-questions-identity-protection-in-azure-active-directory"></a>Azure Active Directory 'de sık sorulan sorular kimlik koruması

## <a name="dismiss-user-risk-known-issues"></a>Kullanıcı riski bilinen sorunlarını kapat

Klasik kimlik koruması ' nda **Kullanıcı riskini** kapatma, kullanıcının kimlik koruması olan risk GEÇMIŞINI **Azure AD**'ye ayarlar.

Kimlik koruması ' nda **Kullanıcı riskini** kapatma kullanıcının **Dikey penceresine\>bir köprü ile yönetici adını\<** için kullanıcının kimlik koruması 'ndaki risk geçmişini ayarlar.

Kullanıcı riski geri ayırma akışında gecikmeye neden olan geçerli bir bilinen sorun var. "Kullanıcı risk ilkeniz" varsa, bu ilke "Kullanıcı riskini kapat" seçeneğine tıklanarak kapatılan kullanıcılara uygulamayı durdurur. Ancak, UX ile kapatılan kullanıcıların "risk durumunu" yenilemesinin bilinen gecikmeleri vardır. Geçici bir çözüm olarak, en son kullanıcıyı "risk durumu" görmek için tarayıcı düzeyindeki sayfayı yenileyin.

## <a name="risky-users-report-known-issues"></a>Riskli kullanıcılar bilinen sorunları raporlar

**Kullanıcı** adı alanındaki sorgular büyük/küçük harfe duyarlıdır, ancak **ad** alanındaki sorgular büyük/küçük harfe duyarlıdır.

**Tarihleri göster** değiştirme, **RISK Son güncelleme** sütununu gizler. Sütunu yeniden eklemek için riskli kullanıcılar dikey penceresinin en üstündeki **sütunlar** ' a tıklayın.

Klasik kimlik korumasında **tüm olayları kapat** risk algılamalarının durumunu **Kapalı (çözüldü)** olarak ayarlar.

## <a name="risky-sign-ins-report-known-issues"></a>Riskli oturum açma işlemleri raporu bilinen sorunlar

Risk algılamasında **Çözümle** , durumu **risk tabanlı ILKEYE göre yönetilen MFA 'ya geçen kullanıcılara**ayarlar.

## <a name="frequently-asked-questions"></a>Sık sorulan sorular

### <a name="why-cant-i-set-my-own-risk-levels-for-each-risk-detection"></a>Her risk algılaması için kendi risk düzeylerimi neden ayarlayamıyorum?

Kimlik koruması 'ndaki risk düzeyleri algılama hassasiyetini temel alır ve denetimli makine öğrenimi tarafından desteklenir. Kullanıcıların hangi deneyimle sunulduğunu özelleştirmek için, Yönetici Kullanıcı risk ve oturum açma risk Ilkelerinden belirli kullanıcıları/grupları dahil edebilir/hariç tutabilir.

### <a name="why-does-the-location-of-a-sign-in-not-match-where-the-user-truly-signed-in-from"></a>Bir oturum açma konumu, kullanıcının gerçekten gerçekten oturum açmasından nasıl eşleşmez?

IP coğrafi coğrafi eşleme, sektör genelinde bir zorluk dır. Oturum açma raporunda listelenen konumun gerçek konumla eşleşmediğinden, Microsoft destek 'e ulaşın. 

### <a name="how-do-the-feedback-mechanisms-in-identity-protection-work"></a>Kimlik koruması 'ndaki geri bildirim mekanizmaları nasıl çalışır?

**Tehlikede olduğunu onaylayın** (oturum açma sırasında) – oturum açma kimliğinin kimlik sahibi tarafından gerçekleştirilmediğini ve bir güvenliğinin aşıldığını belirtir Azure AD kimlik koruması bildirir.

- Bu geri bildirimin alınması sırasında, oturum açma ve Kullanıcı risk durumunu, **güvenliği aşılmış** ve risk düzeyini **yüksek**olarak geçeceğiz.

- Ayrıca, risk değerlendirmesinde gelecekteki geliştirmeler için Machine Learning sistemlerimize bilgi sağlıyoruz.

    > [!NOTE]
    > Kullanıcı zaten düzeltildiyse, oturum açma ve Kullanıcı risk durumunu **onaylanabilecek** ve risk düzeyi **yüksek**olarak taşıdığı için **güvenliği aşılmış** ' i tıklamayın.

**Güvenli** ' i (oturum açma sırasında) onayla – oturum açma 'nın kimlik sahibi tarafından gerçekleştirildiğini Azure AD kimlik koruması ve bir uzlaşmaya Işaret olmadığını bildirir.

- Bu geri bildirimin alınması sırasında, oturum açma (Kullanıcı değil) risk durumunu **onaylı güvenli** ve risk düzeyine ( **-** ) taşıyacağız.

- Ayrıca, risk değerlendirmesinde gelecekteki geliştirmeler için Machine Learning sistemlerimize bilgi sağlıyoruz.

    > [!NOTE]
    > Kullanıcının güvenliğinin aşılmadığını düşünüyorsanız, oturum açma düzeyinde **onaylanmış güvenli** kullanmak yerine Kullanıcı düzeyinde Kullanıcı **riskini kapat** seçeneğini kullanın. Kullanıcı düzeyindeki Kullanıcı **riskini** kapatma, Kullanıcı riskini ve geçmişteki tüm riskli oturum açma işlemlerini ve risk algılamalarını kapatır.

### <a name="why-am-i-seeing-a-user-with-a-low-or-above-risk-score-even-if-no-risky-sign-ins-or-risk-detections-are-shown-in-identity-protection"></a>Kimlik koruması 'nda riskli oturum açma veya risk algılamaları gösterilmese bile, bir kullanıcıyı düşük (veya üzeri) risk puanı ile neden görüyorum?

Kullanıcı riskini doğası gereği, kullanım süreleri dolana kadar, kimlik koruması sırasında son riskli oturum açma işlemleri veya risk algılamaları olmasa bile, bir kullanıcı düşük veya daha yüksek bir Kullanıcı riski oluşturabilir. Bu durum, bir kullanıcının riskli oturum açma işlemlerinin ayrıntılarını ve risk algılamalarını depolayabilmemiz için yalnızca kötü amaçlı etkinlik zaman dilimini aşacak şekilde gerçekleştiyse meydana gelebilir. Kötü aktörlerin, saldırılarına devam etmeden önce güvenliği aşılmış bir kimliğin arkasındaki 140 gün içinde müşterilerin ortamında kalması bilindiği için Kullanıcı riskini sona ermedik. Müşteriler kullanıcının risk zaman çizelgesini inceleyerek, bir kullanıcının neden risk altında olduğunu anlayabilir: `Azure Portal > Azure Active Directory > Risky users’ report > Click on an at-risk user > Details’ drawer > Risk history tab`

### <a name="why-does-a-sign-in-have-a-sign-in-risk-aggregate-score-of-high-when-the-detections-associated-with-it-are-of-low-or-medium-risk"></a>Neden bir oturum açma, bununla ilişkili algılamalar düşük veya orta riskli olduğunda bir "oturum açma riski (toplama)" puanı elde ediyor mu?

Yüksek toplu risk puanı, oturum açma işleminin diğer özelliklerine veya bu oturum açma için birden fazla algılamanın tetiklenmesi durumunda olabilir. Üstelik, oturum açma ile ilişkili algılamalar yüksek riskli olsa da, oturum açma, ortamın bir oturum açma riski (toplama) olabilir. 
