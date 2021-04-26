---
title: Azure Active Directory parolasız oturum açma
description: FIDO2 güvenlik anahtarlarını veya Microsoft Authenticator uygulamasını kullanarak Azure Active Directory için parolasız oturum açma seçenekleri hakkında bilgi edinin
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d939207af195abf117645e17f4ff189b163244f
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107305540"
---
# <a name="passwordless-authentication-options-for-azure-active-directory"></a>Azure Active Directory için passwordless kimlik doğrulama seçenekleri

Multi-Factor Authentication (MFA) gibi özellikler, kuruluşunuzu güvenli hale getirmenin harika bir yoludur, ancak kullanıcılar genellikle parolalarını anımsamak için gereken ek güvenlik katmanını öğreniyor. Parola kaldırılıp, sizin veya bildiğiniz bir şey ile değiştirildiği için passwordless kimlik doğrulama yöntemleri daha uygundur.

| Kimlik Doğrulaması  | Sahip olduğunuz bir şey | Sizin veya bildiğiniz bir şey |
| --- | --- | --- |
| Parolasız | Windows 10 cihaz, telefon veya güvenlik anahtarı | Biyometrik veya PIN |

Her kuruluş, kimlik doğrulamasına geldiğinde farklı gereksinimlere sahiptir. Microsoft, Azure Active Directory (Azure AD) ile tümleştirilen üç adet passwordless kimlik doğrulama seçeneği sunar:

- İş İçin Windows Hello
- Microsoft Authenticator uygulaması
- FIDO2 güvenlik anahtarları

![Kimlik doğrulama: güvenlik ve kolaylık](./media/concept-authentication-passwordless/passwordless-convenience-security.png)

## <a name="windows-hello-for-business"></a>İş İçin Windows Hello

Iş için Windows Hello, kendilerine ait Windows bılgısayar olan bilgi çalışanları için idealdir. Biyometrik ve PIN kimlik bilgileri, kullanıcının BILGISAYARıNA doğrudan bağlanır ve bu da sahip dışında bir kişiye erişimi engeller. Ortak anahtar altyapısı (PKI) tümleştirmesi ve çoklu oturum açma (SSO) için yerleşik destek ile Iş için Windows Hello, şirket içi ve buluttaki kurumsal kaynaklara sorunsuz bir şekilde erişmek için kullanışlı bir yöntem sağlar.

![Iş için Windows Hello ile Kullanıcı oturum açma örneği](./media/concept-authentication-passwordless/windows-hellow-sign-in.jpeg)

Aşağıdaki adımlarda, oturum açma işleminin Azure AD ile nasıl çalıştığı gösterilmektedir:

![Iş için Windows Hello ile Kullanıcı oturumu açma ile ilgili adımları özetleyen diyagram](./media/concept-authentication-passwordless/windows-hello-flow.png)

1. Kullanıcı, Biyometri veya PIN hareketini kullanarak Windows 'da oturum açar. Hareket, Iş için Windows Hello özel anahtarının kilidini açar ve Cloud *AP sağlayıcısı* olarak adlandırılan bulut kimlik doğrulaması güvenlik desteği sağlayıcısına gönderilir.
1. Cloud AP sağlayıcısı, Azure AD 'den bir kerelik anahtar (yalnızca bir kez kullanılabilecek rastgele rastgele bir sayı) ister.
1. Azure AD, 5 dakika boyunca geçerli bir nonce döndürür.
1. Cloud AP sağlayıcısı, kullanıcının özel anahtarını kullanarak nonce 'yi imzalar ve imzalı nonce 'yi Azure AD 'ye döndürür.
1. Azure AD, kullanıcının güvenli şekilde kaydedilmiş ortak anahtarını nonce imzasına karşı kullanarak imzalı nonce 'yi doğrular. İmza doğrulandıktan sonra, Azure AD döndürülen imzalı nonce 'yi doğrular. Nonce doğrulandığında, Azure AD, cihazın aktarım anahtarına şifrelenmiş ve bunu Cloud AP sağlayıcısına döndüren bir birincil yenileme belirteci (PRT) oluşturur.
1. Cloud AP sağlayıcısı, oturum anahtarıyla şifrelenmiş PRT 'yi alır. Bulut AP sağlayıcısı, cihazın özel aktarım anahtarını kullanarak oturum anahtarının şifresini çözer ve cihazın Güvenilir Platform Modülü (TPM) kullanarak oturum anahtarını korur.
1. Cloud AP sağlayıcısı, Windows 'a başarılı bir kimlik doğrulama yanıtı döndürür. Daha sonra Kullanıcı, yeniden kimlik doğrulamasından (SSO) gerek kalmadan Windows 'un yanı sıra bulut ve şirket içi uygulamalara erişebilir.

İş için Windows Hello [Planlama Kılavuzu](/windows/security/identity-protection/hello-for-business/hello-planning-guide) , Iş Için Windows Hello dağıtımı ve göz önünde bulundurmanız gereken seçenekler hakkında kararlar almanıza yardımcı olmak için kullanılabilir.

## <a name="microsoft-authenticator-app"></a>Microsoft Authenticator uygulaması

Ayrıca, çalışanın telefonunun parolasız bir kimlik doğrulama yöntemi olmasına da izin verebilirsiniz. Microsoft Authenticator uygulamasını bir parolanın yanı sıra uygun bir Multi-Factor Authentication seçeneği olarak zaten kullanıyor olabilirsiniz. Kimlik doğrulayıcı uygulamasını passwordless seçeneği olarak da kullanabilirsiniz.

![Microsoft Authenticator uygulamayla Microsoft Edge 'de oturum açın](./media/concept-authentication-passwordless/concept-web-sign-in-microsoft-authenticator-app.png)

Authenticator uygulaması tüm iOS veya Android telefonlarına güçlü, passworddaha az bir kimlik bilgisi getirir. Kullanıcılar, telefonlarına bir bildirim alarak, ekranda görüntülenebilecek bir sayıyla eşleşen bir sayı ile eşleşen ve sonra biyometrik (dokunmatik veya yüz) veya PIN 'i (touch veya yüz) veya PIN 'ini kullanarak herhangi bir platformda veya tarayıcıda oturum açabilirler. Yükleme ayrıntıları için [Microsoft Authenticator uygulamayı indirip](../user-help/user-help-auth-app-download-install.md) yükleme bölümüne bakın.

Kimlik doğrulayıcı uygulamasını kullanan passwordless kimlik doğrulaması, Iş için Windows Hello ile aynı temel kalıbı izler. Azure AD 'nin kullanılan Microsoft Authenticator uygulama sürümünü bulabilmesi için, kullanıcının tanımlanması gerektiğinden çok daha karmaşıktır:

![Microsoft Authenticator uygulamayla Kullanıcı oturumu açma ile ilgili adımları özetleyen diyagram](./media/concept-authentication-passwordless/authenticator-app-flow.png)

1. Kullanıcı Kullanıcı adını girer.
1. Azure AD, kullanıcının güçlü bir kimlik bilgisine sahip olduğunu algılar ve güçlü kimlik bilgisi akışını başlatır.
1. İOS cihazlarında Apple Anında İletilen Bildirim Servisi (APNS) aracılığıyla uygulamaya veya Android cihazlarda Firebase Cloud Messaging (FCM) aracılığıyla bir bildirim gönderilir.
1. Kullanıcı anında iletme bildirimini alır ve uygulamayı açar.
1. Uygulama Azure AD 'yi çağırır ve bir varlık kanıtı ve nonce bilgisi alır.
1. Kullanıcı, özel anahtarın kilidini açmak için biyometrik veya PIN 'ı girerek sınamayı tamamlar.
1. Nonce özel anahtarla imzalanır ve Azure AD 'ye geri gönderilir.
1. Azure AD ortak/özel anahtar doğrulaması gerçekleştirir ve bir belirteç döndürür.

Parolasız oturum açma 'yı kullanmaya başlamak için aşağıdaki nasıl yapılır:

> [!div class="nextstepaction"]
> [Doğrulayıcı uygulamasını kullanarak passwordless işaretini etkinleştirme](howto-authentication-passwordless-phone.md)

## <a name="fido2-security-keys"></a>FIDO2 güvenlik anahtarları

FIDO (hızlı kimlik çevrimiçi) Birliği, açık kimlik doğrulama standartlarının yükseltilebilirliği ve parolaların kullanımını bir kimlik doğrulama formu olarak azaltmaya yardımcı olur. FIDO2, Web kimlik doğrulaması (WebAuthn) standardını içeren en son standarttır.

FIDO2 güvenlik anahtarları, herhangi bir form faktöründe yer alan unphishable standartlara dayalı, passwordless kimlik doğrulama yöntemidir. Hızlı kimlik çevrimiçi (FIDO), passwordless kimlik doğrulaması için açık bir standarttır. FIDO, kullanıcıların ve kuruluşların, bir dış güvenlik anahtarı veya bir cihaza yerleşik bir platform anahtarı kullanarak bir Kullanıcı adı veya parola olmadan kendi kaynaklarında oturum açmasını sağlar.

Kullanıcılar, kimlik doğrulama yöntemi olarak oturum açma arabiriminde bir FIDO2 güvenlik anahtarı kaydedebilir ve seçebilir. Bu FIDO2 güvenlik anahtarları genellikle USB cihazlarıdır, ancak Bluetooth veya NFC de kullanabilir. Kimlik doğrulamasını işleyen bir donanım aygıtıyla, açığa çıkarılan veya tahmin edilecek bir parola olmadığından hesabın güvenliği artar.

FIDO2 güvenlik anahtarları, Azure AD 'de veya hibrit Azure AD 'ye katılmış Windows 10 cihazlarında oturum açmak ve bulutta ve şirket içi kaynaklarda çoklu oturum açma sağlamak için kullanılabilir. Kullanıcılar, desteklenen tarayıcılarda da oturum açabilirler. FIDO2 güvenlik anahtarları, çok güvenliğe duyarlı olan veya telefon numarası ikinci bir faktör olarak kullanabilecek senaryolar veya çalışanlar olan kuruluşlar için harika bir seçenektir.

[Tarayıcıların Azure AD Ile FIDO2 kimlik doğrulamasını desteklediği](fido2-compatibility.md), [GELIŞTIRDIKLERI uygulamalarda FIDO2 kimlik doğrulamasını desteklemek](../develop/support-fido2-authentication.md)isteyen geliştiriciler için en iyi yöntemleri içeren bir başvuru belgesidir.

![Bir güvenlik anahtarıyla Microsoft Edge 'de oturum açın](./media/concept-authentication-passwordless/concept-web-sign-in-security-key.png)

Bir Kullanıcı FIDO2 güvenlik anahtarıyla oturum açtığında aşağıdaki işlem kullanılır:

![FIDO2 güvenlik anahtarıyla Kullanıcı oturum açma için ilgili adımları özetleyen diyagram](./media/concept-authentication-passwordless/fido2-security-key-flow.png)

1. Kullanıcı, FIDO2 güvenlik anahtarını bilgisayarlarına takar.
2. Windows, FIDO2 güvenlik anahtarını algılar.
3. Windows bir kimlik doğrulama isteği gönderir.
4. Azure AD bir nonce geri gönderir.
5. Kullanıcı, FIDO2 güvenlik anahtarının güvenli kuşsında depolanan özel anahtarın kilidini açma hareketini tamamlar.
6. FIDO2 güvenlik anahtarı, anahtar nonce 'i özel anahtarla imzalar.
7. İmzalı nonce ile birincil yenileme belirteci (PRT) belirteç isteği Azure AD 'ye gönderilir.
8. Azure AD, FIDO2 ortak anahtarını kullanarak imzalı nonce 'yi doğrular.
9. Azure AD, şirket içi kaynaklara erişimi etkinleştirmek için PRT 'yi döndürür.

FIDO Alliance tarafından FIDO2 sertifikalı çok sayıda anahtar olmakla kalmaz, Microsoft, en yüksek güvenlik ve en iyi deneyimi sağlamak amacıyla satıcı tarafından uygulanması için FIDO2 Istemci-Authenticator Protokolü (CTAP) belirtiminin bazı isteğe bağlı uzantılarını gerektirir.

Bir güvenlik anahtarı, FIDO2 CTAP protokolünden Microsoft ile uyumlu olmak için aşağıdaki özellikleri ve **uzantıları gerçekleştirmelidir:**

| # | Özellik/uzantı güveni | Bu özellik veya uzantı neden gereklidir? |
| --- | --- | --- |
| 1 | Yerleşik anahtar | Bu özellik, kimlik bilgilerinizin güvenlik anahtarında depolandığı güvenlik anahtarının taşınabilir olmasını sağlar. |
| 2 | İstemci PIN 'i | Bu özellik, kimlik bilgilerinizi ikinci bir faktörle korumanıza olanak sağlar ve Kullanıcı arabirimine sahip olmayan güvenlik anahtarları için geçerlidir. |
| 3 | HMAC-gizli | Bu uzantı, hatta çevrimdışı veya uçak modunda cihazınızda oturum açabilmenizi sağlar. |
| 4 | RP başına birden çok hesap | Bu özellik, Microsoft hesabı ve Azure Active Directory gibi birden çok hizmet arasında aynı güvenlik anahtarını kullanmanıza da sağlar. |

### <a name="fido2-security-key-providers"></a>FIDO2 güvenlik anahtarı sağlayıcıları

Aşağıdaki sağlayıcılar, passwordless deneyimiyle uyumlu oldukları bilinen farklı form faktörlerinin FIDO2 güvenlik anahtarlarını sunmaktadır. Bu anahtarların güvenlik özelliklerini, satıcıya ve FIDO Alliance ile iletişim kurarak değerlendirmeniz önerilir.

| Sağlayıcı | İletişim |
| --- | --- |
| Yılıco | [https://www.yubico.com/solutions/passwordless/](https://www.yubico.com/solutions/passwordless/) |
| Feian | [https://ftsafe.us/pages/microsoft](https://ftsafe.us/pages/microsoft) |
| CIHAZDAN | [https://www.hidglobal.com/contact-us](https://www.hidglobal.com/contact-us) |
| Ensurlik | [https://www.ensurity.com/contact](https://www.ensurity.com/contact) |
| TrustKey çözümleri | [https://www.trustkeysolutions.com/security-keys/](https://www.trustkeysolutions.com/security-keys/) |
| AuthenTrend | [https://authentrend.com/about-us/#pg-35-3](https://authentrend.com/about-us/#pg-35-3) |
| Gemalto (Thales grubu) | [https://safenet.gemalto.com/multi-factor-authentication/authenticators/passwordless-authentication/](https://safenet.gemalto.com/multi-factor-authentication/authenticators/passwordless-authentication/) |
| OneSpan Inc. | [https://www.onespan.com/products/fido](https://www.onespan.com/products/fido) |
| Idmelon Technologies Inc. | [https://www.idmelon.com/#idmelon](https://www.idmelon.com/#idmelon) |
| Hipersecu | [https://www.hypersecu.com/hyperfido](https://www.hypersecu.com/hyperfido) |
| VinCSS | [https://passwordless.vincss.net](https://passwordless.vincss.net) |
| KONA I | [https://konai.com/business/security/fido](https://konai.com/business/security/fido) |
| Excelsecu | [https://www.excelsecu.com/productdetail/esecufido2secu.html](https://www.excelsecu.com/productdetail/esecufido2secu.html) |
| Token2 Isviçre | [https://www.token2.swiss/shop/product/token2-t2f2-alu-fido2-u2f-and-totp-security-key](https://www.token2.swiss/shop/product/token2-t2f2-alu-fido2-u2f-and-totp-security-key) |
| Gotrustıd Inc. | [https://www.gotrustid.com/idem-key](https://www.gotrustid.com/idem-key) |
| Kensington | [https://www.kensington.com/solutions/product-category/why-biometrics/](https://www.kensington.com/solutions/product-category/why-biometrics/) |

> [!NOTE]
> Ve NFC tabanlı güvenlik anahtarları kullanmayı planlıyorsanız, güvenlik anahtarı için desteklenen bir NFC okuyucusuna ihtiyacınız vardır. NFC okuyucusu bir Azure gereksinimi veya sınırlaması değildir. Desteklenen NFC okuyucuları listesi için, NFC tabanlı güvenlik anahtarınızın satıcısına danışın.

Bir satıcısıysanız ve cihazınızı bu desteklenen cihazlar listesinde almak istiyorsanız, iletişim kurun [Fido2Request@Microsoft.com](mailto:Fido2Request@Microsoft.com) .

FIDO2 güvenlik anahtarlarını kullanmaya başlamak için aşağıdaki nasıl yapılır:

> [!div class="nextstepaction"]
> [FIDO2 güvenlik anahtarlarını kullanarak passwordless imzalamayı etkinleştir](howto-authentication-passwordless-security-key.md)

## <a name="supported-scenarios"></a>Desteklenen senaryolar

Aşağıdaki noktalara dikkat edilmelidir:

- Yöneticiler, kiracı için passwordless kimlik doğrulama yöntemlerini etkinleştirebilir
- Yöneticiler tüm kullanıcıları hedefleyebilir veya kiracının her bir yöntemi için kullanıcıları/grupları seçebilir
- Son kullanıcılar, bu passwordless kimlik doğrulama yöntemlerini hesap portalında kaydedebilir ve yönetebilir
- Son kullanıcılar, bu passwordless kimlik doğrulama yöntemleriyle oturum açabilirler:
   - Microsoft Authenticator uygulaması: tüm tarayıcılarda, Windows 10 kurulumu sırasında ve herhangi bir işletim sisteminde tümleşik mobil uygulamalarla birlikte Azure AD kimlik doğrulamasının kullanıldığı senaryolarda çalışmaktadır.
   - Güvenlik anahtarları: Microsoft Edge (hem eski hem de yeni kenar) gibi desteklenen tarayıcılarda Windows 10 ve Web için kilit ekranında çalışın.

## <a name="choose-a-passwordless-method"></a>Passwordless yöntemi seçin

Bu üç parolasız seçenek arasındaki seçim, şirketinizin güvenlik, platform ve uygulama gereksinimlerine bağlıdır.

Microsoft passwordless teknolojisini seçerken göz önünde bulundurmanız gereken bazı etmenler aşağıda verilmiştir:

||**İş İçin Windows Hello**|**Microsoft Authenticator uygulamayla passwordless oturum açma**|**FIDO2 güvenlik anahtarları**|
|:-|:-|:-|:-|
|**Önkoşul**| Windows 10, sürüm 1809 veya üzeri<br>Azure Active Directory| Microsoft Authenticator uygulaması<br>Telefon (Android 6,0 veya üzeri çalıştıran iOS ve Android cihazları.)|Windows 10, sürüm 1903 veya üzeri<br>Azure Active Directory|
|**Mod**|Platform|Yazılım|Donanım|
|**Sistemler ve cihazlar**|Yerleşik Güvenilir Platform Modülü bılgısayar (TPM)<br>PIN ve Biyometri tanıma |Telefonda PIN ve Biyometri tanıma|Microsoft uyumlu FIDO2 güvenlik cihazları|
|**Kullanıcı deneyimi**|Windows cihazlarıyla bir PIN veya biyometrik tanıma (yüz, Iris veya parmak izi) kullanarak oturum açın.<br>Windows Hello kimlik doğrulaması cihaza bağlıdır; kullanıcının şirket kaynaklarına erişmek için hem cihaza hem de PIN veya biyometri faktörü gibi bir oturum açma bileşenine ihtiyacı vardır.|Parmak izi taraması, yüz veya Iris tanıma veya PIN ile cep telefonu kullanarak oturum açın.<br>Kullanıcılar, PC veya cep telefonlarından iş veya kişisel hesap için oturum açabilirler.|FIDO2 güvenlik cihazını (biyometri, PIN ve NFC) kullanarak oturum açın<br>Kullanıcı, cihaza, USB güvenlik anahtarları ve NFC 'nin etkinleştirildiği akıllı kartlar, anahtarlar veya wearables gibi cihazları kullanarak kuruluş denetimlerini ve kimlik doğrulamasını temel alarak cihaz erişimi sağlayabilir.|
|**Etkin senaryolar**| Windows cihazındaki parola-daha az deneyim.<br>Cihaz ve uygulamalarda çoklu oturum açma yeteneğine sahip adanmış iş BILGISAYARı için geçerlidir.|Cep telefonu kullanan parola-daha az her yerde çözüm.<br>Herhangi bir cihazdan Web üzerinde iş veya kişisel uygulamalara erişmek için geçerlidir.|Biyometri, PIN ve NFC kullanan çalışanlar için parola açısından daha az deneyim.<br>Paylaşılan bilgisayarlar ve cep telefonunun uygun bir seçenek olmaması (örneğin, yardım masası personeli, genel bilgi noktası veya hastane ekibi gibi) için geçerlidir|

Gereksinimlerinizi ve kullanıcılarınızı hangi yöntemin destekleyeceği belirlemek için aşağıdaki tabloyu kullanın.

|Bilgisini|Senaryo|Ortam|Passwordless teknolojisi|
|:-|:-|:-|:-|
|**Yönetici**|Yönetim görevleri için bir cihaza güvenli erişim|Atanan Windows 10 cihazı|Iş için Windows Hello ve/veya FIDO2 güvenlik anahtarı|
|**Yönetici**|Windows olmayan cihazlarda yönetim görevleri| Mobil veya Windows dışı cihaz|Microsoft Authenticator uygulamayla passwordless oturum açma|
|**Bilgi çalışanı**|Üretkenlik çalışmaları|Atanan Windows 10 cihazı|Iş için Windows Hello ve/veya FIDO2 güvenlik anahtarı|
|**Bilgi çalışanı**|Üretkenlik çalışmaları| Mobil veya Windows dışı cihaz|Microsoft Authenticator uygulamayla passwordless oturum açma|
|**Frontline Worker**|Bir fabrika, Tesis, perakende veya veri girişinde kiosks|Paylaşılan Windows 10 cihazları|FIDO2 güvenlik anahtarları|

## <a name="next-steps"></a>Sonraki adımlar

Azure AD 'de passwordless kullanmaya başlamak için aşağıdaki nasıl yapılır:

* [FIDO2 güvenlik anahtarı passwordless oturum açma özelliğini etkinleştir](howto-authentication-passwordless-security-key.md)
* [Kimlik doğrulayıcı uygulamasıyla telefon tabanlı parolasız oturum açmayı etkinleştirme](howto-authentication-passwordless-phone.md)

### <a name="external-links"></a>Dış bağlantılar

* [FıDO Alliance](https://fidoalliance.org/)
* [FIDO2 CTAP belirtimi](https://fidoalliance.org/specs/fido-v2.0-id-20180227/fido-client-to-authenticator-protocol-v2.0-id-20180227.html)
