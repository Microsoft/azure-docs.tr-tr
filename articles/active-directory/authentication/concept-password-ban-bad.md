---
title: Azure Active Directory parola koruması
description: Azure Active Directory parola koruması ile ortamınızdaki zayıf parolaları dinamik olarak nasıl arayalabileceğinizi öğrenin
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/16/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 740201b84323f5a01ab01bb21ec458a19832e499
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100652029"
---
# <a name="eliminate-bad-passwords-using-azure-active-directory-password-protection"></a>Parola korumasını Azure Active Directory kullanarak kötü parolaları kaldırın

Birçok güvenlik kılavuzu, aynı parolayı birden fazla yerde kullanmanıza, karmaşık hale getirmek ve *Password123* gibi basit parolalardan kaçınmanızı önerir. Kullanıcılarınıza [parolaların nasıl seçlenebileceğine ilişkin yönergeler](https://www.microsoft.com/research/publication/password-guidance)sağlayabilirsiniz, ancak zayıf veya güvenli olmayan parolalar genellikle hala kullanılır. Azure AD parola koruması, bilinen zayıf parolaları ve bunların türevlerini algılar ve engeller ve ayrıca kuruluşunuza özgü ek zayıf terimleri engelleyebilir.

Azure AD parola koruması ile, varsayılan genel yasaklanmış parola listeleri bir Azure AD kiracısındaki tüm kullanıcılara otomatik olarak uygulanır. Kendi iş ve güvenlik gereksinimlerinizi desteklemek için girişleri özel yasaklanmış bir parola listesinde tanımlayabilirsiniz. Kullanıcılar parolalarını değiştirdiğinde veya sıfırlarsam, bu yasaklanmış parola listeleri, güçlü parolaların kullanımını zorlamak üzere denetlenir.

Azure AD [Multi-Factor Authentication](concept-mfa-howitworks.md)gibi ek özellikler kullanmanız gerekir, yalnızca Azure AD parola koruması tarafından zorlanan güçlü parolalara güvenmez. Oturum açma olaylarınız için birden çok güvenlik katmanını kullanma hakkında daha fazla bilgi için, [PA $ $Word bunun önemi yoktur](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984).

> [!IMPORTANT]
> Bu kavramsal makalede, Azure AD parola korumasının nasıl çalıştığı bir yöneticiye açıklanmaktadır. Self servis parola sıfırlama için zaten kayıtlı bir son kullanıcı varsa ve hesabınıza geri dönmek istiyorsanız adresine gidin [https://aka.ms/sspr](https://aka.ms/sspr) .
>
> BT ekibiniz kendi parolanızı sıfırlama özelliğini etkinleştirmediyseniz, ek yardım için yardım masasına ulaşın.

## <a name="global-banned-password-list"></a>Global yasaklanmış parola listesi

Azure AD Kimlik Koruması takım, Azure AD güvenlik telemetri verilerini sürekli olarak çözümleyerek yaygın olarak kullanılan zayıf veya güvenliği aşılmış parolalara bakar. Özellikle, analiz, genellikle zayıf parolaların temeli olarak kullanılan temel koşulları arar. Zayıf koşullar bulunduğunda, bunlar *genel yasaklanmış parola listesine* eklenir. Genel yasaklanmış parola listesinin içeriği, herhangi bir dış veri kaynağını temel almaz, ancak Azure AD güvenlik telemetri ve analizine ait sonuçlar.

Bir Azure AD kiracısındaki herhangi bir kullanıcı için bir parola değiştirildiğinde veya sıfırlandığında, parolanın gücünü doğrulamak için genel yasaklanmış parola listesinin geçerli sürümü kullanılır. Bu doğrulama denetimi, tüm Azure AD müşterileri için daha güçlü parolalara neden olur.

Genel yasaklanmış parola listesi, bir Azure AD kiracısındaki tüm kullanıcılara otomatik olarak uygulanır. Etkinleştirilecek veya yapılandırılacak bir şey yok ve devre dışı bırakılamaz. Bu genel yasaklanmış parola listesi kullanıcılara Azure AD aracılığıyla kendi parolalarını değiştirdiklerinde veya sıfırlarlarsa uygulanır.

> [!NOTE]
> Cyber-Dolandırıcılar, yaygın zayıf parolaları ve değişimleri belirlemek için saldırılarına karşı benzer stratejileri de kullanır. Microsoft, güvenliği artırmak için genel yasaklanmış parola listesinin içeriğini yayımlamaz.

## <a name="custom-banned-password-list"></a>Özel yasaklanmış parola listesi

Bazı kuruluşlar güvenliği artırmak ve genel yasaklanmış parola listesinin üzerine kendi özelleştirmelerini eklemek ister. Kendi girdilerinizi eklemek için, *özel yasaklanmış parola listesini* kullanabilirsiniz. Özel yasaklanmış parola listesine eklenen koşullar, aşağıdaki örnekler gibi kuruluşa özgü terimlere odaklanmalıdır:

- Marka adları
- Ürün adları
- Şirket yönetim merkezleri gibi konumlar
- Şirkete özgü dahili şartlar
- Belirli bir şirkete anlamı olan kısaltmalar

Özel yasaklanmış parola listesine koşullar eklendiğinde, bunlar genel yasaklanmış parola listesindeki koşullarla birleştirilir. Daha sonra parola değiştirme veya sıfırlama olayları, bu yasaklanmış parola listelerinin birleştirilmiş kümesine göre onaylanır.

> [!NOTE]
> Özel yasaklanmış parola listesi en fazla 1000 terim ile sınırlıdır. Parolaların son derece büyük listesini engellemek için tasarlanmamıştır.
>
> Özel yasaklanmış parola listesinin avantajlarından tamamen yararlanmak için, önce özel yasaklanmış listesine terimler eklemeden önce [parolaların nasıl değerlendirileceğini](#how-are-passwords-evaluated) anlayın. Bu yaklaşım, çok sayıda zayıf parolayı ve bunların türevlerini etkin bir şekilde algılayıp engellemeyi sağlar.

![Kimlik doğrulama yöntemleri altındaki özel yasaklanmış parola listesini değiştirme](./media/tutorial-configure-custom-password-protection/enable-configure-custom-banned-passwords-cropped.png)

*Contoso* adlı bir müşteriyi ele alalım. Şirket Londra ' a dayalıdır ve *pencere öğesi* adında bir ürün oluşturur. Bu örnek müşteri için, aşağıdaki gibi, bu koşulların belirli çeşitlemelerini engellemeyi denemek daha az güvenli ve daha az güvenlidir:

- "Contoso! 1"
- "Contoso@London"
- "Contosopencere öğesi"
- "! 'Nun
- "LondonHQ"

Bunun yerine, aşağıdaki örnekler gibi yalnızca anahtar temel koşullarını engellemek çok daha etkilidir ve güvenlidir:

- "Contoso"
- Londra
- Pencere öğesi

Parola doğrulama algoritması, zayıf çeşitleri ve birleşimleri otomatik olarak engeller.

Özel yasaklanmış parola listesini kullanmaya başlamak için aşağıdaki öğreticiyi izleyin:

> [!div class="nextstepaction"]
> [Öğretici: özel yasaklanmış parolaları yapılandırma](tutorial-configure-custom-password-protection.md)

## <a name="password-spray-attacks-and-third-party-compromised-password-lists"></a>Parola püskürtme saldırıları ve üçüncü taraf güvenliği aşılmış parola listeleri

Azure AD parola koruması, parola ilaç saldırılarına karşı savunmanıza yardımcı olur. Birçok parola spreyi saldırısı, belirli bir hesaba birkaç kez saldırmayı denemeyin. Bu davranış, hesap kilitleme ya da başka yollarla algılama olasılığını artırır.

Bunun yerine, parola spreyi saldırılarının çoğunluğu bir kuruluştaki hesapların her birine karşı yalnızca küçük sayıda bilinen zayıf parolayı gönderir. Bu teknik, saldırganın kolayca güvenliği aşılmış bir hesabı hızla aramasını ve olası algılama eşiklerini önlemeyi sağlar.

Azure AD parola koruması, bilinen tüm zayıf parolaların parola püskürtme saldırılarına karşı kullanılmasını verimli bir şekilde engeller. Bu koruma, genel yasaklanmış parola listesini oluşturmak için Azure AD 'den gerçek dünyada güvenlik telemetri verilerini temel alır.

Yaygın olarak bilinen güvenlik ihlallerinin tehlikeye girdiği milyonlarca parolayı gösteren bazı üçüncü taraf web siteleri vardır. Üçüncü taraf parola doğrulama ürünlerinin, bu milyonlarca parolalara karşı deneme yanılma karşılaştırması temelinde olması yaygındır. Ancak, bu teknikler, parola sprey saldırganlar tarafından kullanılan tipik stratejilere verilen genel parola gücünü geliştirmenin en iyi yolu değildir.

> [!NOTE]
> Genel yasaklanmış parola listesi, güvenliği aşılmış parola listeleri dahil olmak üzere herhangi bir üçüncü taraf veri kaynağını temel almaz.

Global yasaklanmış listesi bazı üçüncü taraf toplu listelerine karşılaştırılmış olsa da, gerçek parola spreyi saldırılarına açık olan gerçek dünyada güvenlik telemetrisinden kaynaklıdır. Bu yaklaşım genel güvenlik ve verimliliği artırır ve parola doğrulama algoritması, akıllı benzer eşleştirme tekniklerini de kullanır. Sonuç olarak, Azure AD parola koruması, kuruluşunuzda en sık kullanılan zayıf parolaların milyonlarca kullanımını verimli bir şekilde algılar ve engeller.

## <a name="on-premises-hybrid-scenarios"></a>Şirket içi karma senaryolar

Birçok kuruluşun şirket içi Active Directory Domain Services (AD DS) ortamları içeren bir karma kimlik modeli vardır. Azure AD parola korumasının güvenlik avantajlarını AD DS ortamınıza uzatmak için, bileşenleri şirket içi sunucularınıza yükleyebilirsiniz. Bu aracılar, Azure AD ile aynı parola ilkesiyle uyum sağlamak için şirket içi AD DS ortamında parola değiştirme olayları gerektirir.

Daha fazla bilgi için bkz. [Azure AD parola korumasını AD DS Için zorlama](concept-password-ban-bad-on-premises.md).

## <a name="how-are-passwords-evaluated"></a>Parolalar nasıl değerlendirilir

Kullanıcı parolasını değiştirdiğinde veya sıfırladığında yeni parola, genel ve özel yasaklanmış parola listelerinden oluşan Birleşik terim listesine karşı doğrulayarak güç ve karmaşıklık açısından denetlenir.

Kullanıcının parolası yasaklanmış bir parola içerse de, genel parolanın yeterince güçlü olması durumunda parola kabul edilebilir. Yeni yapılandırılan bir parola, kabul edilip edilmesinin gerekip gerekmediğini belirlemek için genel gücünü değerlendirmek üzere aşağıdaki adımlardan geçer:

### <a name="step-1-normalization"></a>1. Adım: normalleştirme

Yeni bir parola, önce bir normalleştirme işleminden geçer. Bu teknik, küçük bir yasaklanmış parola kümesinin daha büyük olabilecek çok zayıf parolalara eşlenmesine olanak tanır.

Normalleştirme aşağıdaki iki bölümden oluşur:

* Tüm büyük harfler küçük harfe dönüştürülür.
* Ardından, aşağıdaki örnekte olduğu gibi ortak karakter alternatifleri gerçekleştirilir:

   | Özgün harf | Değiştirilen mektup |
   |-----------------|--------------------|
   | 0               | o                  |
   | 1               | l                  |
   | $               | s                  |
   | \@              | a                  |

Aşağıdaki örneği inceleyin:

* "Blank" parolası yasaklanmış.
* Kullanıcı parolasını "" olarak değiştirmeye çalışır Bl@nK .
* " Bl@nk " Yasaklanmış olmamasına rağmen, normalleştirme işlemi bu parolayı "boş" olarak dönüştürür.
* Bu parola reddedilir.

### <a name="step-2-check-if-password-is-considered-banned"></a>2. Adım: parolanın yasaklanmış olarak kabul edildiğinden emin olun

Daha sonra başka eşleşen davranış için bir parola incelenir ve bir puan oluşturulur. Bu son puan, parola değiştirme isteğinin kabul edildiğini veya reddedildiğini belirler.

#### <a name="fuzzy-matching-behavior"></a>Belirsiz eşleştirme davranışı

Belirsiz eşleştirme, genel veya özel yasaklanmış parola listelerinde bulunan bir parola içerip içermesinin belirlenmesi için normalleştirilmiş parolada kullanılır. Eşleşen işlem, bir (1) karşılaştırmayla ilgili düzenleme mesafesini temel alır.

Aşağıdaki örneği inceleyin:

* "Abcdef" parolası yasaklanmış.
* Bir Kullanıcı, parolasını aşağıdakilerden biriyle değiştirmeye çalışır:

   * ' abcdeg '- *son karakter ' f ' iken ' g ' olarak değiştirildi*
   * ' abcdefg '- *' g ' sonuna eklendi*
   * ' abcde '- *sondaki ' f ' sonda silindi*

* Yukarıdaki parolaların her biri, yasaklanmış "abcdef" parolasıyla özellikle eşleşmez.

    Ancak, her örnek yasaklanmış ' abcdef ' teriminin 1. bir düzenleme uzaklığı içinde olduğundan, hepsi "abcdef" ile eşleşme olarak kabul edilir.
* Bu parolalar reddedilir.

#### <a name="substring-matching-on-specific-terms"></a>Alt dize eşleştirme (belirli koşullara göre)

Dize eşleştirme, kullanıcının adı ve soyadı ve kiracı adının yanı sıra kullanıcının adını ve soyadını denetlemek için normalleştirilmiş parolada kullanılır. Şirket içi karma senaryolar için AD DS etki alanı denetleyicisindeki parolaları doğrularken kiracı adı eşleştirme yapılmaz.

> [!IMPORTANT]
> Alt dize eşleştirme yalnızca, en az dört karakter uzunluğunda olan adlar ve diğer terimler için zorlanır.

Aşağıdaki örneği inceleyin:

* Parolasını "p0LL23fb" olarak sıfırlamak isteyen yoklama adlı Kullanıcı.
* Normalleştirme sonrasında bu parola "poll23fb" olur.
* Alt dize eşleştirme, parolanın kullanıcının ilk adını "yoklama" içerdiğini bulur.
* "Poll23fb" özellikle yasaklanmış parola listesinde olmadığı halde, alt dize eşleştirme, parolada "yoklama" buldu.
* Bu parola reddedilir.

#### <a name="score-calculation"></a>Puan hesaplaması

Sonraki adım kullanıcının normalleştirilmiş yeni parolalarındaki tüm yasaklanmış parolaların örneklerini belirlemektir. Noktaları aşağıdaki ölçütlere göre atanır:

1. Kullanıcının parolasıdır bulunan her yasaklanmış parolanın tek bir noktası verilir.
1. Yasaklanmış bir parolanın parçası olmayan kalan her karaktere bir nokta verilir.
1. Parolanın kabul edilmesi için en az beş (5) Punto olması gerekir.

Contoso, sonraki iki örnek senaryo için Azure AD parola korumasını kullanıyor ve özel yasaklanmış parola listesinde "contoso" içeriyor. Ayrıca "boş" öğesinin genel listede olduğunu varsayalım.

Aşağıdaki örnek senaryoda, bir Kullanıcı parolasını "C0ntos0Blank12" olarak değiştirir:

* Normalleştirme sonrasında bu parola "contosoblank12" olur.
* Eşleşen işlem bu parolanın iki yasaklanmış parola içerdiğini bulur: "contoso" ve "Blank".
* Bu parolaya daha sonra aşağıdaki puan verilir:

    *[contoso] + [boş] + [1] + [2] = 4 punto*

* Bu parola beş (5) Punto altındayken reddedilir.

Bir parolada ek karmaşıklığın kabul edilecek gereken sayıda noktayı göstermek için biraz farklı bir örneğe bakalım. Aşağıdaki örnek senaryoda, bir Kullanıcı parolasını " ContoS0Bl@nkf9 !" olarak değiştirir:

* Normalleştirme sonrasında bu parola "contosoblankf9!" olur.
* Eşleşen işlem bu parolanın iki yasaklanmış parola içerdiğini bulur: "contoso" ve "Blank".
* Bu parolaya daha sonra aşağıdaki puan verilir:

    *[contoso] + [boş] + [f] + [9] + [!] = 5 punto*

* Bu parola en az beş (5) Punto olduğu için kabul edilir.

> [!IMPORTANT]
> Yasaklanmış parola algoritması, genel yasaklanmış parola listesiyle birlikte, sürekli güvenlik Analizi ve araştırmaya dayalı olarak Azure 'da herhangi bir zamanda değiştirilebilir ve değişebilir.
>
> Karma senaryolarda şirket içi DC Aracısı hizmeti için, güncelleştirilmiş algoritmalar yalnızca DC Aracısı yazılımı yükseltildikten sonra devreye girer.

## <a name="what-do-users-see"></a>Kullanıcıların ne görecek

Bir Kullanıcı parolayı yasaklanmış bir şekilde sıfırlamayı denediğinde aşağıdaki hata iletisi görüntülenir:

*"Maalesef parolanız parolanızı kolayca tahmin eden bir sözcük, tümcecik veya model içeriyor. Lütfen farklı bir parola ile yeniden deneyin. "*

## <a name="license-requirements"></a>Lisans gereksinimleri

| Kullanıcılar | Genel yasaklanmış parola listesiyle Azure AD parola koruması | Özel yasaklanmış parola listesiyle Azure AD parola koruması|
|-------------------------------------------|---------------------------|---------------------------|
| Yalnızca bulutta bulunan kullanıcılar                          | Azure AD Ücretsiz             | Azure AD Premium P1 veya P2 |
| Şirket içi AD DS eşitlenen kullanıcılar | Azure AD Premium P1 veya P2 | Azure AD Premium P1 veya P2 |

> [!NOTE]
> Azure AD ile eşitlenmemiş kullanıcılar, Azure AD parola korumasından de eşitlenmiş kullanıcıların mevcut lisanslarına göre de faydalanır. AD DS

[Azure Active Directory fiyatlandırma sitesinde](https://azure.microsoft.com/pricing/details/active-directory/)maliyetler de dahil olmak üzere ek lisans bilgileri bulunabilir.

## <a name="next-steps"></a>Sonraki adımlar

Özel yasaklanmış parola listesini kullanmaya başlamak için aşağıdaki öğreticiyi izleyin:

> [!div class="nextstepaction"]
> [Öğretici: özel yasaklanmış parolaları yapılandırma](tutorial-configure-custom-password-protection.md)

Ayrıca, [Şirket Içi Azure AD parola korumasını etkinleştirebilirsiniz](howto-password-ban-bad-on-premises-deploy.md).
