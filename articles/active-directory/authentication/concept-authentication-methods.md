---
title: Kimlik doğrulama yöntemleri-Azure Active Directory
description: MFA ve SSPR için Azure AD 'de kullanılabilir kimlik doğrulama yöntemleri
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/16/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry, michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 18eba93661d9c418a230ced8f9970047a869a7e3
ms.sourcegitcommit: ec2b75b1fc667c4e893686dbd8e119e7c757333a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72808419"
---
# <a name="what-are-authentication-methods"></a>Kimlik doğrulaması yöntemleri nelerdir?

Yönetici olarak, Azure Multi-Factor Authentication ve self servis parola sıfırlama (SSPR) için kimlik doğrulama yöntemlerini seçme, kullanıcıların birden çok kimlik doğrulama yöntemini kaydetmesini gerektirmesini öneririz. Bir kullanıcı için kimlik doğrulama yöntemi kullanılabilir olmadığında, başka bir yöntemle kimlik doğrulaması seçebilirler.

Yöneticiler, SSPR ve MFA kullanıcıları tarafından hangi kimlik doğrulama yöntemlerinin kullanılabildiği ilke ' de tanımlayabilir. Bazı kimlik doğrulama yöntemleri, tüm özellikler için kullanılamayabilir. İlkelerinizi yapılandırma hakkında daha fazla bilgi için [self servis parola sıfırlama](howto-sspr-deployment.md) ve [bulut tabanlı bir Azure Multi-Factor Authentication planlama](howto-mfa-getstarted.md) makalesine bakın.

Microsoft, yöneticilerin erişimi olmayan en az sayıda kimlik doğrulama yönteminden fazlasını seçmesini olanaklı kılmanızı sağlar.

|Kimlik Doğrulama Yöntemi|Kullanım|
| --- | --- |
| Parola | MFA ve SSPR |
| Güvenlik soruları | Yalnızca SSPR |
| E-posta adresi | Yalnızca SSPR |
| Microsoft Authenticator uygulaması | MFA ve SSPR |
| OATH donanım belirteci | MFA ve SSPR için genel önizleme |
| SMS | MFA ve SSPR |
| Sesli çağrı | MFA ve SSPR |
| Uygulama parolaları | Yalnızca belirli durumlarda MFA |

![Oturum açma ekranında kullanılan kimlik doğrulama yöntemleri](media/concept-authentication-methods/overview-login.png)

|     |
| --- |
| MFA ve SSPR için OATH Donanım belirteçleri, Azure Active Directory genel önizleme özellikleridir. Önizlemeler hakkında daha fazla bilgi için bkz. [Microsoft Azure önizlemeleri Için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="password"></a>Parola

Azure AD parolanız bir kimlik doğrulama yöntemi olarak kabul edilir. Bu, **devre dışı**bırakıladesteklemeyen bir yöntemdir.

## <a name="security-questions"></a>Güvenlik soruları

Güvenlik soruları **yalnızca Azure AD self servis parola sıfırlama ' da** yönetici olmayan hesaplara kullanılabilir.

Güvenlik sorularını kullanıyorsanız, bunları başka bir yöntemle birlikte kullanmanızı öneririz. Bazı kullanıcılar başka bir Kullanıcı sorusunun yanıtlarını bilebileceğinden, güvenlik soruları diğer yöntemlerden daha az güvenli olabilir.

> [!NOTE]
> Güvenlik soruları, dizindeki bir kullanıcı nesnesi üzerinde özel olarak ve güvenli bir şekilde depolanır ve yalnızca kayıt sırasında kullanıcılar tarafından yanıtlanabilecek. Bir yöneticinin bir kullanıcının sorularını veya yanıtlarını okuması veya değiştirmesi için bir yol yoktur.
>

### <a name="predefined-questions"></a>Önceden tanımlanmış sorular

* İlk eşiniz/iş ortağınızı hangi şehirle karşıladınız?
* Ebeveynleriniz hangi şehirle buluşmış?
* En yakın eşdüzey ağınız hangi şehirde etkin?
* Babalar hangi şehirde doğdu?
* İlk işiniz hangi şehirdir?
* Anneniz hangi şehirde doğdu?
* Yeni yıl 2000 ' de hangi şehirdeydiniz?
* Yüksek okulda en sevdiğiniz öğretmenin soyadı nedir?
* Uyguladığınız bir üniversite adı nedir, ancak katılmadınız mı?
* İlk evlilik alıyeninizi dağıttığınız yerin adı nedir?
* Babalar ikinci adı nedir?
* En sevdiğiniz yemek nedir?
* Anneannenizin en Annenizin adı ve soyadı nedir?
* Annenizin ikinci adı nedir?
* En eski eşdüzey Doğum günün ayı ve yılı nedir? (örn. Kasım 1985)
* En eski eşdüzey öğenin ikinci adı nedir?
* Babanızın babalar adı ve soyadı nedir?
* Kardeşinizin eşdüzey ortaınızın ikinci adı nedir?
* Altıncı sınıf için hangi okula katıldınız?
* Çocukluğunuzdaki en iyi arkadaşınızın adı ve soyadı neydi?
* İlk önemli olan ilk ve soyadı neydi?
* En sevdiğiniz sınıf okul öğretmeninizin soyadı neydi?
* İlk arabalarınızın veya otodöngünüzün marka ve modeli neydi?
* Katıldığınız ilk okulunuzun adı neydi?
* Doğmakta olduğunuz hastanın adı neydi?
* İlk çocukluk evin sokak adı neydi?
* Çocukluğunuzdaki kahraman 'ın adı neydi?
* En sevdiğiniz peluş ya da bir hayvan adı neydi?
* İlk evcil hayvanınızın adı neydi?
* Çocukluk takma ad nedir?
* Yüksek okulda en sevdiğiniz spor nedir?
* İlk işiniz neydi?
* Çocukluk telefon numaranız için son dört basamak neydi?
* Küçükken, büyüyken ne yapmak istiyorsunuz?
* Şimdiye kadar tanıştığınız en ünlü kişi kim?

Önceden tanımlanmış tüm güvenlik soruları, kullanıcının tarayıcı yerel ayarlarına bağlı olarak Office 365 dillerinin tam kümesine çevrilir ve yerelleştirilir.

### <a name="custom-security-questions"></a>Özel güvenlik soruları

Özel güvenlik soruları yerelleştirilmez. Kullanıcının tarayıcı yerel ayarı farklı olsa bile, tüm özel sorular Yönetici Kullanıcı arabirimine girildikleri dilde görüntülenir. Yerelleştirilmiş sorulara ihtiyacınız varsa, önceden tanımlanmış soruları kullanmanız gerekir.

Özel bir güvenlik sorusunun en fazla uzunluğu 200 karakterdir.

### <a name="security-question-requirements"></a>Güvenlik sorusu gereksinimleri

* En küçük yanıt karakter sınırı üç karakterdir.
* En fazla yanıt karakter sınırı 40 karakterdir.
* Kullanıcılar aynı sorudan birden çok kez yanıt vermez.
* Kullanıcılar, birden fazla soruya aynı yanıtı sağlayamaz.
* Herhangi bir karakter kümesi, Unicode karakterler de dahil olmak üzere soruları ve yanıtları tanımlamak için kullanılabilir.
* Tanımlanan soruların sayısı, kaydolmak için gereken soru sayısına eşit veya ondan büyük olmalıdır.

## <a name="email-address"></a>E-posta adresi

E-posta adresi **yalnızca Azure AD self servis parola sıfırlama ' da**kullanılabilir.

Microsoft, kullanıcının Azure AD parolasının erişmesini gerektirmeyen bir e-posta hesabının kullanılmasını önerir.

## <a name="microsoft-authenticator-app"></a>Microsoft Authenticator uygulaması

Microsoft Authenticator uygulaması, Azure AD iş veya okul hesabınız veya Microsoft hesabı için ek bir güvenlik düzeyi sağlar.

Microsoft Authenticator uygulaması [Android](https://go.microsoft.com/fwlink/?linkid=866594), [iOS](https://go.microsoft.com/fwlink/?linkid=866594) ve [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6)'da kullanılabilir.

> [!NOTE]
> Kullanıcılar self servis parola sıfırlama için kaydolurken mobil uygulamalarını kaydetme seçeneğine sahip olmayacaktır. Bunun yerine, kullanıcılar mobil uygulamalarını [https://aka.ms/mfasetup](https://aka.ms/mfasetup) veya güvenlik bilgileri kayıt önizlemesine [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo)adresinde kaydedebilir.
>

### <a name="notification-through-mobile-app"></a>Mobil uygulama aracılığıyla bildirim

Microsoft Authenticator uygulaması, Smartphone 'a veya tabletinize bildirim göndererek, hesaplara yetkisiz erişimin önlenmesine ve sahte işlemleri durdurmaya yardımcı olabilir. Kullanıcılar bildirimi görüntüler ve meşru ise Doğrula ' yı seçin. Aksi takdirde, Reddet ' i seçebilir.

> [!WARNING]
> Yalnızca bir yöntem sıfırlama için gerekliyse, self servis parola sıfırlama için, **en yüksek düzeyde güvenlik sağlamak üzere**kullanıcılara sunulan tek seçenektir.
>
> İki yöntem gerekli olduğunda, kullanıcılar diğer etkin yöntemlerin yanı **sıra bildirim** **veya** doğrulama kodu kullanarak sıfırlayabilecektir.
>

Mobil uygulama ve doğrulama kodu aracılığıyla her iki bildirimin de kullanımını etkinleştirirseniz, bir bildirim kullanarak Microsoft Authenticator uygulamasını kaydeden kullanıcılar kimliklerini doğrulamak için hem bildirimi hem de kodu kullanabilir.

> [!NOTE]
> Kuruluşunuzda Çin 'de çalışan veya Çin 'e geçiş yapan personel varsa, **Android cihazlarda** **mobil uygulama yöntemi ile ilgili bildirim** söz konusu ülkede çalışmaz. Bu kullanıcılar için alternatif yöntemler kullanılabilir hale gelmelidir.

### <a name="verification-code-from-mobile-app"></a>Mobil uygulamadaki doğrulama kodu

Microsoft Authenticator uygulaması veya diğer üçüncü taraf uygulamalar, bir OATH doğrulama kodu oluşturmak için yazılım belirteci olarak kullanılabilir. Kullanıcı adınızı ve parolanızı girdikten sonra, uygulama tarafından belirtilen kodu oturum açma ekranına girersiniz. Doğrulama kodu, ikinci bir kimlik doğrulama biçimi sağlar.

> [!WARNING]
> Kendi kendine parola sıfırlama için yalnızca bir yöntem gerekliyse, sıfırlama doğrulama kodu için yalnızca bir yöntem gerekliyse, **en yüksek düzeyde güvenlik sağlamak için**kullanıcılara sunulan tek seçenektir.
>

Kullanıcılar, her zaman kullanılmak üzere yapılandırılmış Microsoft Authenticator uygulaması gibi beş OATH donanım belirtecinin veya Authenticator uygulamasının bir birleşimine sahip olabilir.

## <a name="oath-hardware-tokens-public-preview"></a>OATH Donanım belirteçleri (Genel Önizleme)

OATH, bir kerelik parola (OTP) kodlarının nasıl oluşturulduğunu belirten bir açık standarttır. Azure AD, 30 saniyelik veya 60 saniyelik birçok farklı OATH-TOTP SHA-1 belirteçleri kullanımını destekleyecektir. Müşteriler bu belirteçleri tercih ettikleri satıcıdan temin edebilir. Gizli anahtarlar 128 karakterle sınırlıdır ve bu, tüm belirteçlerle uyumlu olmayabilir. Gizli anahtarların Base32 içinde kodlanmış olması gerekir.

![OATH belirteçleri MFA sunucusu OATH belirteçleri dikey penceresine yükleniyor](media/concept-authentication-methods/oath-tokens-azure-ad.png)

OATH Donanım belirteçleri, genel önizlemenin bir parçası olarak desteklenmektedir. Önizlemeler hakkında daha fazla bilgi için bkz. [Microsoft Azure önizlemeleri Için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

Belirteçler alındıktan sonra, aşağıdaki örnekte gösterildiği gibi, UPN, seri numarası, gizli anahtar, zaman aralığı, üretici ve model dahil olmak üzere bir virgülle ayrılmış değerler (CSV) dosya biçiminde karşıya yüklenmelidir.

```csv
upn,serial number,secret key,time interval,manufacturer,model
Helga@contoso.com,1234567,1234567890abcdef1234567890abcdef,60,Contoso,HardwareKey
```

> [!NOTE]
> Üst bilgi satırını, yukarıda gösterildiği gibi CSV dosyanıza eklediğinizden emin olun.

Bir CSV dosyası olarak düzgün biçimlendirildikten sonra, bir yönetici Azure portal oturum açabilir ve **Azure Active Directory**, **MFA sunucusu**, **Oath belirteçleri**' ne gidebilir ve elde edilen CSV dosyasını karşıya yükleyebilir.

CSV dosyasının boyutuna bağlı olarak, işlem birkaç dakika sürebilir. Geçerli durumu almak için **Yenile** düğmesine tıklayın. Dosyada herhangi bir hata varsa, çözmeniz için herhangi bir hata listelemesi için bir CSV dosyası indirme seçeneğine sahip olursunuz.

Herhangi bir hata çözüldükten sonra, yönetici, belirtecin etkinleştirilebilmesi için **Etkinleştir** ' e tıklayarak ve BELIRTEÇTE görünen OTP 'yi girerek her anahtarı etkinleştirebilir.

Kullanıcılar, her zaman kullanılmak üzere yapılandırılmış Microsoft Authenticator uygulaması gibi beş OATH donanım belirtecinin veya Authenticator uygulamasının bir birleşimine sahip olabilir.

## <a name="phone-options"></a>Telefon seçenekleri

### <a name="mobile-phone"></a>Cep telefonu

Mobil telefonlarla kullanıcılara iki seçenek mevcuttur.

Kullanıcılar cep telefonu numarasının dizinde görünür olmasını istemiyor, ancak yine de parola sıfırlama için kullanmak istiyorlarsa, yöneticiler onu dizinde doldurmamalıdır. Kullanıcılar, **kimlik doğrulama telefonu** özniteliğini [parola sıfırlama kayıt portalı](https://aka.ms/ssprsetup)aracılığıyla doldurmalıdır. Yöneticiler bu bilgileri kullanıcının profilinde görebilir, ancak başka bir yerde yayımlanmaz.

Doğru şekilde çalışmak için telefon numaralarının *+ CountryCode PhoneNumber*biçiminde olması gerekir, örneğin + 1 4255551234.

> [!NOTE]
> Ülke kodu ile telefon numarası arasında bir boşluk olması gerekir.
>
> Parola sıfırlama, telefon uzantılarını desteklemez. \+ 1 4255551234X12345 biçiminde bile, çağrı yerleştirilmadan önce uzantılar kaldırılır.

Microsoft, tutarlı SMS veya sesli tabanlı Multi-Factor Authentication istemi teslimini aynı numarayla garanti etmez. Kullanıcılarımız konusunda, Microsoft, SMS teslimat yeteneğini geliştirmek üzere rota ayarlamaları yaptığımız için, her zaman kısa kodlar ekleyebilir veya kaldırabilir. Microsoft, Birleşik Devletler ve Kanada yanı sıra ülkeler/bölgeler için kısa kodları desteklemez.

#### <a name="text-message"></a>Kısa mesaj

Bir SMS, bir doğrulama kodu içeren cep telefonu numarasına gönderilir. Devam etmek için oturum açma arabiriminde belirtilen doğrulama kodunu girin.

#### <a name="phone-call"></a>Telefon araması

Sağladığınız telefon numarasına otomatik bir sesli çağrı yapılır. Numarayı yanıtlayın ve telefon tuş takımında # tuşlarına basarak kimlik doğrulaması yapın

> [!IMPORTANT]
> Mart 2019 ' den itibaren telefon araması seçenekleri ücretsiz/deneme Azure AD kiracılarında MFA ve SSPR kullanıcıları tarafından kullanılamaz. SMS iletileri bu değişiklikten etkilenmez. Telefon araması, ücretli Azure AD kiracılarındaki kullanıcılar için kullanılabilir olmaya devam edecektir. Bu değişiklik yalnızca ücretsiz/deneme Azure AD kiracılarını etkiler.

### <a name="office-phone"></a>Ofis telefonu

Sağladığınız telefon numarasına otomatik bir sesli çağrı yapılır. Numarayı yanıtlayın ve telefon tuş takımında # tuşlarına bastıktan sonra kimlik doğrulaması yapın.

Doğru şekilde çalışmak için telefon numaralarının *+ CountryCode PhoneNumber*biçiminde olması gerekir, örneğin + 1 4255551234.

Ofis telefonu özniteliği yöneticiniz tarafından yönetilir.

> [!IMPORTANT]
> Mart 2019 ' den itibaren telefon araması seçenekleri ücretsiz/deneme Azure AD kiracılarında MFA ve SSPR kullanıcıları tarafından kullanılamaz. SMS iletileri bu değişiklikten etkilenmez. Telefon araması, ücretli Azure AD kiracılarındaki kullanıcılar için kullanılabilir olmaya devam edecektir. Bu değişiklik yalnızca ücretsiz/deneme Azure AD kiracılarını etkiler.

> [!NOTE]
> Ülke kodu ile telefon numarası arasında bir boşluk olması gerekir.
>
> Parola sıfırlama, telefon uzantılarını desteklemez. \+ 1 4255551234X12345 biçiminde bile, çağrı yerleştirilmadan önce uzantılar kaldırılır.

### <a name="troubleshooting-phone-options"></a>Telefon seçenekleri sorunlarını giderme

Telefon numarası kullanan kimlik doğrulama yöntemleriyle ilgili yaygın sorunlar:

* Tek bir cihazda engellenen arayan KIMLIĞI
   * Cihaz sorunlarını giderme
* Yanlış telefon numarası, yanlış ülke kodu, ev telefonu numarası ve iş telefonu numarası
   * Kullanıcı nesnesi ve yapılandırılmış kimlik doğrulama yöntemleriyle ilgili sorunları giderin. Doğru telefon numaralarının kaydedildiğinden emin olun.
* Yanlış PIN girildi
   * Kullanıcının Azure MFA sunucusu 'nda kayıtlı doğru PIN 'ı kullandığını onaylayın.
* Sesli mesaj ile iletilen çağrı
   * Kullanıcının telefonu açık olduğundan ve bu hizmetin kendi alanında kullanılabilir olduğundan emin olun veya alternatif yöntemi kullanın.
* Kullanıcı engellendi
   * Yöneticinin Azure portal kullanıcının engellemesini kaldırma.
* Cihazda SMS abone değil
   * Kullanıcının bir yöntemi değiştirmesini veya cihazda SMS 'yi etkinleştirmesini sağlamak.
* Hatalı Telekom sağlayıcıları (telefon girişi algılanmadı, eksik DTMF tonları sorunları, birden çok cihazda engellenen çağıran KIMLIĞI veya birden çok cihazda engellenen SMS)
   * Microsoft, kimlik doğrulaması için telefon çağrılarını ve SMS iletilerini yönlendirmek üzere birden çok Telekom sağlayıcısı kullanır Yukarıdaki sorunlardan herhangi birini görüyorsanız, bir Kullanıcı, 5 dakika içinde yöntemi en az 5 kez kullanmaya çalışır ve Microsoft Destek ile iletişim kurarken bu kullanıcının bilgilerinin kullanılabilir olmasını sağlar.

## <a name="app-passwords"></a>Uygulama parolaları

Tarayıcı olmayan bazı uygulamalar Multi-Factor Authentication 'ı desteklemez, bir Kullanıcı Multi-Factor Authentication için etkinleştirildiyse ve tarayıcı olmayan uygulamaları kullanmaya çalışırsanız, kimlik doğrulaması yapamaz. Uygulama parolası, kullanıcıların kimlik doğrulamaya devam etmesine izin verir

Kullanıcı başına MFA aracılığıyla değil, koşullu erişim ilkeleri aracılığıyla Multi-Factor Authentication zorunlu kılabilirsiniz, uygulama parolaları oluşturamazsınız. Erişimi denetlemek için koşullu erişim ilkelerini kullanan uygulamalar, uygulama parolalara gerek kalmaz.

Kuruluşunuz Azure AD ile SSO için federe ise ve Azure MFA 'yı kullanacaksanız, aşağıdaki ayrıntıları göz önünde bulundurun:

* Uygulama parolası Azure AD tarafından doğrulanır ve bu nedenle Federasyonu atlar. Federasyon yalnızca uygulama parolaları ayarlanırken kullanılır. Federasyon (SSO) kullanıcıları için, parolalar kuruluş KIMLIĞINDE depolanır. Kullanıcı şirketten ayrılırsa, bu bilgi DirSync kullanarak kuruluş KIMLIĞINE akacaktır. Hesabın devre dışı bırakılması/silinmesi üç saate kadar zaman alabilir. Bu, Azure AD 'de uygulama parolalarının devre dışı bırakılması/silinmesi gecikmeyebilir.
* Şirket için İstemci Erişimi Denetimi ayarları Uygulama Parolası tarafından onaylanmaz.
* Uygulama parolaları için şirket içi kimlik doğrulama günlüğü/Denetim yeteneği kullanılamaz.
* Bazı gelişmiş mimari tasarımları, kimlik doğrulamalarına bağlı olarak istemcilerle iki aşamalı doğrulamayı kullanırken kurumsal Kullanıcı adı ve parolaların ve uygulama parolalarının bir birleşimini kullanmanızı gerektirebilir. Şirket içi altyapıya karşı kimlik doğrulaması yapan istemciler için bir kuruluş Kullanıcı adı ve parolası kullanırsınız. Azure AD kimlik doğrulaması yapan istemciler için uygulama parolasını kullanırsınız.
* Varsayılan olarak, kullanıcılar uygulama parolaları oluşturamaz. Kullanıcıların uygulama parolaları oluşturmalarına izin vermeniz gerekiyorsa, hizmet ayarları altındaki **tarayıcı olmayan uygulamalarda oturum açmak için kullanıcıların uygulama parolaları oluşturmasına Izin ver seçeneğini** belirleyin.

## <a name="next-steps"></a>Sonraki adımlar

[Kuruluşunuz için self servis parola sıfırlamayı etkinleştirin](quickstart-sspr.md)

[Kuruluşunuz için Azure Multi-Factor Authentication etkinleştirme](howto-mfa-getstarted.md)

[Kiracınızda Birleşik kayıt özelliğini etkinleştirme](howto-registration-mfa-sspr-combined.md)

[Son Kullanıcı kimlik doğrulama yöntemi yapılandırma belgeleri](https://aka.ms/securityinfoguide)
