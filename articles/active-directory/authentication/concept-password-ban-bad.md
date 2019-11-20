---
title: Dinamik olarak yasaklanmış parolalar-Azure Active Directory
description: Azure AD dinamik olarak yasaklanmış parolalarla ortamınızdaki zayıf parolalar
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 27530b143e46acad4152e8333836cbe9c79fab17
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74168085"
---
# <a name="eliminate-bad-passwords-in-your-organization"></a>Kuruluşunuzdaki hatalı parolaları kaldırın

Sektör liderleri, birden çok yerde aynı parolayı kullanmacağınızı, karmaşık hale getirmek ve "Password123" gibi basit hale getirmek için kullanmayı söylemez. Kuruluşlar, kullanıcılarının en iyi yöntem kılavuzunu takip etmesini güvence altına alabilir mi? Kullanıcıların zayıf parolalar kullanmadığından ve hatta zayıf parolalarda çeşitliliğe sahip olduklarından emin olabilirler.

Daha güçlü parolalara sahip olan ilk adım kullanıcılarınıza rehberlik sağlamaktır. Microsoft 'un bu konuyla ilgili geçerli Kılavuzu aşağıdaki bağlantıda bulunabilir:

[Microsoft parola Kılavuzu](https://www.microsoft.com/research/publication/password-guidance)

İyi bir kılavuzluk olması önemlidir, ancak pek çok kullanıcının hala zayıf parolalar seçeceğimizi bilmemiz gerekir. Azure AD parola koruması, bilinen zayıf parolaları ve türevlerini algılayıp engelleyerek ve isteğe bağlı olarak kuruluşunuza özgü ek zayıf koşulları engelleyerek kuruluşunuzu korur.

Geçerli güvenlik çabaları hakkında daha fazla bilgi için bkz. [Microsoft Güvenlik Intelligence Report](https://www.microsoft.com/security/operations/security-intelligence-report).

## <a name="global-banned-password-list"></a>Global yasaklanmış parola listesi

Azure AD Kimlik Koruması ekibi, yaygın olarak kullanılan zayıf veya riskli parolalara veya daha belirgin bir şekilde, genellikle zayıf parolaların temeli olarak kullanılan zayıf temel koşullara göre, Azure AD güvenlik telemetri verilerini sürekli olarak analiz eder. Bu zayıf koşullar bulunduğunda, bunlar genel yasaklanmış parola listesine eklenir. Genel yasaklanmış parola listesinin içeriği herhangi bir dış veri kaynağını temel almaz. Global yasaklanmış parola listesi tamamen Azure AD güvenlik telemetri ve analizinin devam eden sonuçlarına dayalıdır.

Azure AD 'de herhangi bir kiracıda herhangi bir kullanıcı için yeni bir parola değiştirildiğinde veya sıfırlandığında, parolanın gücünü doğrularken anahtar girişi olarak genel yasaklanmış parola listesinin geçerli sürümü kullanılır. Bu doğrulama, tüm Azure AD müşterileri için çok daha güçlü parolalara neden olur.

> [!NOTE]
> Cyber-Dolandırıcılar, saldırılarına karşı benzer stratejileri de kullanır. Bu nedenle Microsoft bu listenin içeriğini herkese açık bir şekilde yayımlamaz.

## <a name="custom-banned-password-list"></a>Özel yasaklanmış parola listesi

Bazı kuruluşlar, Microsoft 'un özel yasaklanmış parola listesini çağırdığı genel yasaklanmış parola listesinin üzerine kendi özelleştirmelerini ekleyerek güvenliği daha da geliştirmek isteyebilir. Microsoft bu listeye eklenen koşulların birincil olarak kuruluşa özgü koşullara odaklanmasını önerir, örneğin:

- Marka adları
- Ürün adları
- Konumlar (örneğin, Şirket merkez ofisi)
- Şirkete özgü dahili şartlar
- Belirli bir şirkete ilişkin anlamı olan kısaltmalar.

Özel yasaklanmış parola listesine koşullar eklendikten sonra, parolalar doğrulanırken genel yasaklanmış parola listesindeki koşullara göre birleştirilir.

> [!NOTE]
> Özel yasaklanmış parola listesi en fazla 1000 terim olacak şekilde sınırlıdır. Parolaların son derece büyük listesini engellemek için tasarlanmamıştır. Özel yasaklanmış parola listesinin avantajlarından tamamen yararlanmak için, Microsoft, özel yasaklanmış listesine yeni terimler eklemeden önce parola değerlendirme algoritmasını (bkz. [parolaları nasıl değerlendirdiğini](concept-password-ban-bad.md#how-are-passwords-evaluated)) incelemenizi ve anlamasını önerir. Algoritmanın nasıl çalıştığını anlamak, kuruluşunuzun çok sayıda zayıf parolayı ve türevlerini verimli bir şekilde algılamasını ve engellemesini sağlar.

Örneğin: "contoso" adlı bir müşteriyi, Londra 'ya dayalı ve "pencere öğesi" adlı bir ürün yapan bir müşteriyi düşünün. Böyle bir müşteri söz konusu olduğunda, bu koşulların belirli çeşitlemelerini engellemeyi denemek daha az güvenlidir:

- "Contoso! 1"
- "Contoso@London"
- "Contosopencere öğesi"
- "! 'Nun
- "LondonHQ"
- ... etcetera

Bunun yerine, yalnızca anahtar temel koşullarını engellemek çok daha verimlidir ve güvenlidir:

- 'Nun
- Londra
- Pencere öğesi

Parola doğrulama algoritması, yukarıdaki zayıf çeşitleri ve birleşimleri otomatik olarak engeller.

Özel yasaklanmış parola listesi ve şirket içi Active Directory tümleştirmesini etkinleştirme özelliği Azure portal kullanılarak yönetilir.

![Kimlik doğrulama yöntemleri altındaki özel yasaklanmış parola listesini değiştirme](./media/concept-password-ban-bad/authentication-methods-password-protection.png)

## <a name="password-spray-attacks-and-third-party-compromised-password-lists"></a>Parola püskürtme saldırıları ve üçüncü taraf güvenliği aşılmış parola listeleri

Tek bir Azure AD parola koruması avantajı, parola püskürtme saldırılarına karşı savunmanız açısından yardımcı olur. Çoğu parola püskürtme saldırısı, hesap kilitleme veya başka yollarla algılama olasılığını büyük ölçüde arttığı için, bu tür bir hesap, belirli bir hesaba birkaç kez saldırmayı denemez. Bu nedenle, bir kuruluştaki hesapların her birine karşı yalnızca küçük sayıda bilinen zayıf parolanın gönderilmesi, parola spreyi saldırılarına maruz kalmalıdır. Bu teknik, saldırganın, olası algılama eşiklerinden kaçınırken kolayca güvenliği aşılmış bir hesabı hızla aramasını sağlar.

Azure AD parola koruması, Azure AD tarafından görülen gerçek dünyada güvenlik telemetri verilerine bağlı olarak, parola spreyi saldırılarına karşı kullanılan tüm bilinen zayıf parolaları etkin bir şekilde engelleyecek şekilde tasarlanmıştır.  Microsoft, yaygın olarak bilinen güvenlik ihlallerinin tehlikeye girdiği milyonlarca parolayı belirten üçüncü taraf web sitelerinin farkındadır. Üçüncü taraf parola doğrulama ürünlerinin bu milyonlarca parolalara karşı deneme yanılma karşılaştırmasına dayalı olması yaygındır. Microsoft bu tür tekniklerin, parola spreyi tarafından kullanılan tipik stratejileri verilen genel parola gücünü geliştirmenin en iyi yolu değildir.

> [!NOTE]
> Microsoft Global yasaklanmış parola listesi, güvenliği aşılmış parola listeleri dahil olmak üzere herhangi bir üçüncü taraf veri kaynağına bağlı değildir.

Microsoft Global yasaklanmış listesi, bazı üçüncü taraf toplu listelerine karşılaştırılmış olsa da, güvenlik etkileri gerçek parola ilaç saldırılarına karşı gerçek bir güvenlik telemetrisinden kaynaklandığından ve Microsoft 'un parola doğrulama algoritması, akıllı benzer eşleştirme tekniklerini kullanır. Nihai sonuç, kuruluşunuzda en sık kullanılan zayıf parolaların milyonlarca kullanımını etkili bir şekilde algılayıp engelleyecektir. Özel yasaklanmış parola listesine kuruluşa özgü terimler eklemeyi seçen müşteriler aynı algoritmadan da faydalanır.

Parola tabanlı güvenlik sorunlarıyla ilgili ek bilgiler, [PA $ $Word](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984)tarafından incelenmeyebilir.

## <a name="on-premises-hybrid-scenarios"></a>Şirket içi karma senaryolar

Yalnızca bulut hesaplarının korunması yararlı olur ancak birçok kuruluş, şirket içi Windows Server Active Directory dahil olmak üzere Karma senaryolar yaşar. Azure AD parola korumasının güvenlik avantajları, şirket içi aracıların yüklenmesi yoluyla Windows Server Active Directory ortamınıza da genişletilebilir. Artık Active Directory parolalarını değiştiren veya sıfırlayan kullanıcılar ve Yöneticiler yalnızca bulutta bulunan kullanıcılarla aynı parola ilkesiyle uyum sağlamak için gereklidir.

## <a name="how-are-passwords-evaluated"></a>Parolalar nasıl değerlendirilir

Kullanıcı parolasını değiştirdiğinde veya sıfırladığında yeni parola, genel ve özel yasaklanmış parola listelerinden (ikincisi yapılandırılmışsa) Birleşik koşullar listesine karşı doğrulayarak güç ve karmaşıklık açısından denetlenir.

Kullanıcının parolası yasaklanmış bir parola içerse bile, genel parola yeterince güçlü olursa parola yine de kabul edilebilir. Yeni yapılandırılan bir parola, kabul edilip edilmesinin gerekip gerekmediğini belirlemek için genel gücünü değerlendirmek üzere aşağıdaki adımlara geçer.

### <a name="step-1-normalization"></a>1\. Adım: normalleştirme

Yeni bir parola, önce bir normalleştirme işleminden geçer. Bu teknik, küçük bir yasaklanmış parola kümesinin daha büyük olabilecek çok zayıf parolalara eşlenmesine olanak tanır.

Normalleştirme iki bölümden oluşur.  İlk olarak, tüm büyük harfler küçük harfe dönüştürülür.  İkinci olarak, ortak karakter alternatifleri gerçekleştirilir, örneğin:  

| Özgün harf  | Değiştirilen mektup |
| --- | --- |
| 0  | 'h |
| 1  | ölçek |
| '$'  | üreticinin |
| '\@'  | 'm |

Örnek: "Blank" parolasının yasaklanmış olduğunu ve bir kullanıcının parolasını "Bl@nK" olarak değiştirmeye çalıştığını varsayın. "Bl@nk" özellikle yasaklanmış olsa da, normalleştirme süreci bu parolayı yasaklanmış bir parola olan "boş" olarak dönüştürür.

### <a name="step-2-check-if-password-is-considered-banned"></a>2\. Adım: parolanın yasaklanmış olarak kabul edildiğinden emin olun

#### <a name="fuzzy-matching-behavior"></a>Belirsiz eşleştirme davranışı

Belirsiz eşleştirme, genel veya özel yasaklanmış parola listelerinde bulunan bir parola içerip içermesinin belirlenmesi için normalleştirilmiş parolada kullanılır. Eşleşen işlem, bir (1) karşılaştırmayla ilgili düzenleme mesafesini temel alır.  

Örnek: "abcdef" parolasının yasaklanmış olduğunu ve bir kullanıcının parolasını aşağıdakilerden biriyle değiştirmeye çalıştığını varsayın:

' abcdeg ' *(son karakter ' f ' iken ' g ' olarak değiştirildi)* ' abcdefg ' *' (bitiş* ' f ') ' abcde ' *(sondaki ' f ' sonunda silindi)*

Yukarıdaki parolaların her biri, yasaklanmış "abcdef" parolasıyla özellikle eşleşmez. Ancak, her örnek yasaklanmış ' abcdef ' teriminin 1. bir düzenleme uzaklığı içinde olduğundan, hepsi "abcdef" ile eşleşme olarak kabul edilir.

#### <a name="substring-matching-on-specific-terms"></a>Alt dize eşleştirme (belirli koşullara göre)

Alt dize eşleştirme, kullanıcının adını ve soyadını denetlemek için normalleştirilmiş parolada, kiracı adının yanı sıra bir Active Directory etki alanı denetleyicisindeki parolaları doğrularken de kiracı adı eşleştirme yapılmadığını unutmayın.

Örnek: parolasını "P0l123fb" olarak sıfırlamak isteyen bir kullanıcı olan bir kullanıcı olduğunu varsayalım. Normalleştirme sonrasında bu parola "pol123fb" olur. Alt dize eşleştirme, parolanın kullanıcının ilk adını "pol" içerdiğini bulur. "P0l123fb" özellikle yasaklanmış parola listesinde olmamasına rağmen, alt dize eşleştirme parolada "pol" buldu. Bu nedenle bu parola reddedilir.

#### <a name="score-calculation"></a>Puan hesaplaması

Sonraki adım kullanıcının normalleştirilmiş yeni parolalarındaki tüm yasaklanmış parolaların örneklerini belirlemektir. Ardından:

1. Bir kullanıcının parolasıdır bulunan her yasaklanmış parolanın tek bir noktası verilir.
2. Kalan her benzersiz karaktere bir nokta verilir.
3. Parola kabul edilebilmesi için en az beş (5) punto olmalıdır.

Sonraki iki örnek için Contoso 'nun Azure AD parola korumasını kullandığını ve özel listesinde "contoso" olduğunu varsayalım. Ayrıca "boş" öğesinin genel listede olduğunu varsayalım.

Örnek: bir Kullanıcı, parolasını "C0ntos0Blank12" olarak değiştirir

Normalleştirme sonrasında bu parola "contosoblank12" olur. Eşleşen işlem bu parolanın iki yasaklanmış parola içerdiğini bulur: contoso ve Blank. Bu parolaya daha sonra bir puan verilir:

[contoso] + [boş] + [1] + [2] = 4 punto bu parola beş (5) Punto altında olduğundan reddedilecek.

Örnek: bir Kullanıcı, parolasını "ContoS0Bl@nkf9!" olarak değiştirir.

Normalleştirme sonrasında bu parola "contosoblankf9!" olur. Eşleşen işlem bu parolanın iki yasaklanmış parola içerdiğini bulur: contoso ve Blank. Bu parolaya daha sonra bir puan verilir:

[contoso] + [boş] + [f] + [9] + [!] = 5 punto bu parola en az beş (5) puntodan bu yana kabul edilir.

   > [!IMPORTANT]
   > Lütfen genel liste ile yasaklanmış parola algoritmasının, sürekli güvenlik Analizi ve araştırmaya dayalı olarak Azure 'da dilediğiniz zaman değişiklik yapıp yapamadığını unutmayın. Şirket içi DC Aracısı hizmeti için, güncelleştirilmiş algoritmalar yalnızca DC Aracısı yazılımı yeniden yüklendikten sonra devreye girer.

## <a name="license-requirements"></a>Lisans gereksinimleri

|   | Genel yasaklanmış parola listesiyle Azure AD parola koruması | Özel yasaklanmış parola listesiyle Azure AD parola koruması|
| --- | --- | --- |
| Yalnızca bulutta bulunan kullanıcılar | Azure AD Ücretsiz | Azure AD Premium P1 veya P2 |
| Şirket içi Windows Server Active Directory eşitlenen kullanıcılar | Azure AD Premium P1 veya P2 | Azure AD Premium P1 veya P2 |

> [!NOTE]
> Şirket içi Windows Server Active Directory, aynı zamanda eşitlenmiş kullanıcılara yönelik mevcut lisanslama temelinde Azure AD parola korumasından de faydalanan Azure Active Directory.

[Azure Active Directory fiyatlandırma sitesinde](https://azure.microsoft.com/pricing/details/active-directory/)maliyetler de dahil olmak üzere ek lisans bilgileri bulunabilir.

## <a name="what-do-users-see"></a>Kullanıcıların ne görecek

Bir Kullanıcı yasaklanmış olabilecek bir parolayı sıfırlama girişiminde bulunduğunda, şu hata iletisini görürler:

Ne yazık ki parolanız, parolanızın kolayca tahmin edilebilir olmasını sağlayan bir sözcük, tümcecik veya model içerir. Lütfen farklı bir parola ile yeniden deneyin.

## <a name="next-steps"></a>Sonraki adımlar

- [Özel yasaklanmış parola listesini yapılandırın](howto-password-ban-bad.md)
- [Şirket içinde Azure AD parola koruma aracılarını etkinleştirme](howto-password-ban-bad-on-premises-deploy.md)
