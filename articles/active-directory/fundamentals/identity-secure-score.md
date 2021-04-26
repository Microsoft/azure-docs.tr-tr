---
title: Kimlik güvenli puanı nedir? -Azure Active Directory
description: Dizininizin güvenlik duruşunu artırmak için kimlik güvenli Puanını nasıl kullanabileceğinizi öğrenin
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 03/23/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: guptashi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 23832d9f1205105f1f9711cdf3260b74ee4a9bb1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104952272"
---
# <a name="what-is-the-identity-secure-score-in-azure-active-directory"></a>Azure Active Directory kimlik güvenli puanı nedir?

Azure AD kiracınız ne düzeyde güvenli? Bu sorunun nasıl yanıtlandığını bilmiyorsanız, bu makalede kimlik güvenli puanı 'nın kimlik güvenliği sonrası durunuzu izlemenize ve iyileştirebilmenizi nasıl yardımcı olduğu açıklanmaktadır.

## <a name="what-is-an-identity-secure-score"></a>Kimlik güvenliği puanı nedir?

Kimlik güvenli puanı, Microsoft 'un güvenlik için en iyi yöntem önerileriyle nasıl hizalandığı hakkında bir gösterge olarak işlev gören yüzdesidir. Kimlik güvenli puanı 'ndaki her geliştirme eylemi, özel yapılandırmanıza göre tasarlanmıştır.  

![Güvenlik puanı](./media/identity-secure-score/identity-secure-score-overview.png)

Puanın yardımıyla:

- Kimlik güvenliği duruşunuzu nesnel olarak ölçebilirsiniz
- Kimlik güvenliği geliştirmelerini planlayabilirsiniz
- Geliştirmelerinizin başarısını gözden geçirebilirsiniz

Puana ve ilgili bilgilere kimlik güvenliği puanı panosundan erişebilirsiniz. Bu panoda şunları bulursunuz:

- Kimliğiniz güvenli puanı
- Kimlik güvenli puanınızın aynı sektör ve benzer boyuttaki diğer kiracılar ile nasıl Karşılaştırıldığı gösteren bir karşılaştırma grafiği
- Kimlik güvenli puanınızın zaman içinde nasıl değiştiğini gösteren bir eğilim grafiği
- Olası geliştirmelerin listesi

Geliştirme eylemlerini izleyerek:

- Güvenlik sonrası ve puanınızı geliştirme
- Kimlik yatırımlarınızın bir parçası olarak kuruluşunuzun kullanabildiği özelliklerden yararlanın

## <a name="how-do-i-get-my-secure-score"></a>Güvenlik puanımı nasıl alabilirim?

Kimlik güvenli puanı Azure AD 'nin tüm sürümlerinde kullanılabilir. Kuruluşlar, kimlik güvenli puanlarına **Azure Portal**  >  **Azure Active Directory**  >  **güvenlik**  >  **kimliği güvenli puanı** aracılığıyla erişebilir.

## <a name="how-does-it-work"></a>Nasıl çalışır?

Her 48 saatte bir Azure güvenlik yapılandırmanıza bakar ve sizin ayarlarınızı önerilen en iyi yöntemlerle karşılaştırır. Bu değerlendirmenin sonucuna bağlı olarak, dizininiz için yeni bir puan hesaplanır. Güvenlik yapılandırmanızın en iyi yöntem kılavuzıyla tam olarak hizalanması ve geliştirme eylemlerinin yalnızca kısmen karşılanması olasıdır. Bu senaryolarda, yalnızca denetim için kullanılabilir olan en fazla puanı elde edersiniz.

Her öneri, Azure AD yapılandırmanız temelinde ölçülür. En iyi uygulama önerisini etkinleştirmek için üçüncü taraf ürünleri kullanıyorsanız, bu yapılandırmayı bir geliştirme eylemi ayarlarında belirtebilirsiniz. Ayrıca ortamınız için uygulanamazlar, önerileri yok sayılacak şekilde ayarlama seçeneğiniz de vardır. Yoksayılan öneri puanınızın hesaplanmasına katılmaz.

![Üçüncü tarafın kapsamına giren eylemi Yoksay veya işaretle](./media/identity-secure-score/identity-secure-score-ignore-or-third-party-reccomendations.png)

## <a name="how-does-it-help-me"></a>Bana nasıl yardımcı olur?

Güvenlik puanının yardımıyla:

- Kimlik güvenliği duruşunuzu nesnel olarak ölçebilirsiniz
- Kimlik güvenliği geliştirmelerini planlayabilirsiniz
- Geliştirmelerinizin başarısını gözden geçirebilirsiniz

## <a name="what-you-should-know"></a>Bilmeniz gerekenler

### <a name="who-can-use-the-identity-secure-score"></a>Kimlik güvenliği puanını kimler kullanabilir?

Kimlik güvenliği puanı aşağıdaki roller tarafından kullanılabilir:

- Genel yönetici
- Güvenlik yöneticisi
- Güvenlik okuyucuları

### <a name="how-are-controls-scored"></a>Denetimler nasıl puanlanır?

Denetimler iki şekilde puanlanır. Bazıları ikili bir biçimde puanlanır. önerimize göre yapılandırılmış özellik veya ayar varsa puanın %100 ' i elde edersiniz. Diğer puanlar Toplam yapılandırmanın yüzdesi olarak hesaplanır. Örneğin, iyileştirme önerisi, tüm kullanıcılarınızı MFA ile koruduysanız ve yalnızca 5/100 Toplam Kullanıcı korunuyorsa, yalnızca 5 ' e kadar% 10,71 ' a kadar bir kısmi 0,53 puan elde edersiniz (5 korunan/100 Total * 10,71% maksimum = 0,53% kısmi puan).

### <a name="what-does-not-scored-mean"></a>[Puanlanmadı] ne anlama geliyor?

[Puanlanmadı] olarak etiketlenen eylemler, kuruluşunuzda gerçekleştirebileceğiniz ama araca bağlanmadığından (henüz!) puanlanmayacak olan eylemlerdir. Dolayısıyla, yine de güvenliğinizi geliştirebilirsiniz ama şu anda bu eylemler için puan almazsınız.

### <a name="how-often-is-my-score-updated"></a>Puanım ne sıklıkta güncelleştirilir?

Puan günde bir kez hesaplanır (yaklaşık 24:00 PST). Ölçülen eylemde değişiklik yaparsanız, puan ertesi gün otomatik olarak güncelleştirilir. Değişikliğin puanınıza yansıması 48 saat kadar sürebilir.

### <a name="my-score-changed-how-do-i-figure-out-why"></a>Puanım değişti. Nedenini nasıl anlayabilirim?

Tüm Microsoft güvenli puanınızı bulacağınız [Microsoft 365 Güvenlik Merkezi](https://security.microsoft.com/)' ne gidin. Geçmiş sekmesindeki ayrıntılı değişiklikleri inceleyerek güvenli puanınızın tüm değişikliklerini kolayca görebilirsiniz.

### <a name="does-the-secure-score-measure-my-risk-of-getting-breached"></a>Güvenli Puanlama, ihlal alma riskimin nedeniyle mi ölçülemidir?

Kısacası, hayır. Güvenli puan, ihlal alma olasıınızın mutlak bir ölçüsünü ifade etmez. Güvenlik ihlaline uğrama riskini azaltabilecek özellikleri ne düzeyde benimsediğinizi ortaya koyar. Hizmet yok, ihlal edilemez ve güvenli puan herhangi bir şekilde garanti olarak yorumlanmamalıdır.

### <a name="how-should-i-interpret-my-score"></a>Puanımı nasıl yorumlayabilirim?

Puanınız önerilen güvenlik özelliklerini yapılandırma veya güvenlikle ilgili görevleri (raporları okuma gibi) gerçekleştirmeye yönelik olarak geliştirilmiştir. Kullanıcılarınız için Multi-Factor Authentication'ı (MFA) etkinleştirmeniz gibi bazı eylemler, kısmen tamamlanmış olarak puanlanır. Güvenli puanınız, kullandığınız Microsoft güvenlik hizmetlerinin doğrudan temsilcisidir. Güvenliğin kullanılabilirlik ile dengelenmesi gerektiğini unutmayın. Tüm güvenlik denetimlerinin bir de kullanıcı etkisi bileşeni vardır. Düşük kullanıcı etkisine sahip denetimlerin, kullanıcılarınızın gündelik işlemleri üzerinde çok az etkisi olması veya hiç olmaması gerekir.

Puan geçmişinizi görmek için [Microsoft 365 Güvenlik Merkezi](https://security.microsoft.com/) ' ne gidin ve genel Microsoft güvenli puanınızı gözden geçirin. Görünüm geçmişi ' ne tıklayarak genel güvenli puanınızın değişikliklerini gözden geçirebilirsiniz. Belirli bir tarih seçerek o gün hangi denetimlerin etkinleştirildiğini ve her birinden kaç puan kazandığınızı görün.

### <a name="how-does-the-identity-secure-score-relate-to-the-microsoft-365-secure-score"></a>Kimlik güvenli puanı, Microsoft 365 güvenli puanı ile ilgilidir mi?

[Microsoft güvenli puanı](/office365/securitycompliance/microsoft-secure-score) beş farklı denetim ve puan kategorisi içerir:

- Kimlik
- Veriler
- Cihazlar
- Altyapı
- Uygulamalar

Kimlik güvenli puanı, Microsoft güvenli puanı 'nın kimlik bölümünü temsil eder. Bu örtüşme, kimlik güvenli puanı ve Microsoft 'taki kimlik puanı için önerilerin aynı olduğu anlamına gelir.

## <a name="next-steps"></a>Sonraki adımlar

[Microsoft güvenli puanı hakkında daha fazla bilgi edinin](/office365/securitycompliance/microsoft-secure-score)