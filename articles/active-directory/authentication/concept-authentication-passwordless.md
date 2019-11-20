---
title: Azure Active Directory parolasız oturum açma (Önizleme)
description: FIDO2 güvenlik anahtarlarını veya Microsoft Authenticator uygulamasını (Önizleme) kullanarak Azure AD 'de passwordless oturum açma
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 10/08/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: e073b4ff366c05cdf429f81d46647cd330604057
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74081571"
---
# <a name="passwordless-authentication-options"></a>Passwordless kimlik doğrulama seçenekleri

Multi-Factor Authentication (MFA), kuruluşunuzu güvenli hale getirmenin harika bir yoludur, ancak kullanıcılar parolalarını anımsamak için gereken ek katman ile daha kolay bir şekilde yararlanar. Parola kaldırıldığı ve sizin veya bildiğiniz bir şeyi içeren bir şekilde değiştirildiği için passwordless kimlik doğrulama yöntemleri daha uygundur.

|   | Sahip olduğunuz bir şey | Sizin veya bildiğiniz bir şey |
| --- | --- | --- |
| Parolasız | Windows 10 cihaz, telefon veya güvenlik anahtarı | Biyometrik veya PIN |

Her kuruluş, kimlik doğrulamasına geldiğinde farklı gereksinimlere sahiptir. Microsoft üç adet parolasız kimlik doğrulama seçeneği sunar:

- İş İçin Windows Hello
- Microsoft Authenticator uygulaması
- FIDO2 güvenlik anahtarları

![Kimlik doğrulama: güvenlik ve kolaylık](./media/concept-authentication-passwordless/passwordless-convenience-security.png)

## <a name="windows-hello-for-business"></a>İş İçin Windows Hello

Iş için Windows Hello, kendilerine ait Windows bılgısayar olan bilgi çalışanları için idealdir. Biyometrik ve PIN, kullanıcının BILGISAYARıNA doğrudan bağlanır, bu da sahip dışında bir kişiye erişimi engeller. PKI tümleştirmesi ve çoklu oturum açma (SSO) için yerleşik destek sayesinde Iş için Windows Hello, şirket içi ve buluttaki kurumsal kaynaklara sorunsuz bir şekilde erişmek için basit ve kullanışlı bir yöntem sağlar.

İş için Windows Hello [Planlama Kılavuzu](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-planning-guide) , Iş Için Windows Hello dağıtımı ve göz önünde bulundurmanız gereken seçenekler hakkında kararlar almanıza yardımcı olmak için kullanılabilir.

## <a name="microsoft-authenticator-app"></a>Microsoft Authenticator uygulaması

Çalışanın telefonunun parolasız kimlik doğrulama yöntemi olmasına izin verin. Microsoft Authenticator uygulamasını bir parolanın yanı sıra uygun bir Multi-Factor Authentication seçeneği olarak zaten kullanıyor olabilirsiniz. Ancak şimdi, passwordless seçeneği olarak kullanılabilir.

![Microsoft Authenticator uygulamayla Microsoft Edge 'de oturum açın](./media/concept-authentication-passwordless/concept-web-sign-in-microsoft-authenticator-app.png)

Tüm iOS veya Android telefonlarını, kullanıcıların telefonda bir bildirim alarak, telefonda bir sayıyla ve daha sonra biyometrik kullanımlarını kullanarak herhangi bir platformda veya tarayıcıda oturum açmasına izin vererek, güçlü, passworddaha az bir kimlik bilgisi olarak etkinleştirir. onaylamak için dokunmatik veya yüz) veya PIN.

## <a name="fido2-security-keys"></a>FIDO2 güvenlik anahtarları

FIDO2 güvenlik anahtarları, herhangi bir form faktöründe yer alan unphishable standartlara dayalı, passwordless kimlik doğrulama yöntemidir. Hızlı kimlik çevrimiçi (FIDO), passwordless kimlik doğrulaması için açık bir standarttır. Kullanıcıların ve kuruluşların, bir dış güvenlik anahtarı veya bir cihaza yerleşik bir platform anahtarı kullanarak bir Kullanıcı adı veya parola olmadan kendi kaynaklarında oturum açmasını sağlamak için standart kullanmasına olanak sağlar.

Çalışanlar, genel önizleme için Azure AD 'ye katılmış Windows 10 cihazlarında oturum açmak ve bulutta ve şirket içi kaynaklarda çoklu oturum açmak için güvenlik anahtarlarını kullanabilir. Ayrıca, desteklenen tarayıcılarda oturum açabilir.

![Bir güvenlik anahtarıyla Microsoft Edge 'de oturum açın](./media/concept-authentication-passwordless/concept-web-sign-in-security-key.png)

FIDO Alliance tarafından FIDO2 sertifikalı çok sayıda anahtar olmakla kalmaz, Microsoft en yüksek güvenliği ve en iyi şekilde emin olmak için, FIDO2 Istemci-Authenticator Protokolü (CTAP) belirtiminin bazı isteğe bağlı uzantılarını satıcı tarafından uygulanması gerekir deneyimleri.

Bir güvenlik anahtarı, FIDO2 CTAP protokolünden Microsoft ile uyumlu olmak için aşağıdaki özellikleri ve **uzantıları gerçekleştirmelidir:**

| # | Özellik/uzantı güveni | Bu özellik veya uzantı neden gereklidir? |
| --- | --- | --- |
| 1 | Yerleşik anahtar | Bu özellik, kimlik bilgilerinizin güvenlik anahtarında depolandığı güvenlik anahtarının taşınabilir olmasını sağlar. |
| 2 | İstemci PIN 'i | Bu özellik, kimlik bilgilerinizi ikinci bir faktörle korumanıza olanak sağlar ve Kullanıcı arabirimine sahip olmayan güvenlik anahtarları için geçerlidir. |
| 3 | HMAC-gizli | Bu uzantı, hatta çevrimdışı veya uçak modunda cihazınızda oturum açabilmenizi sağlar. |
| 4 | RP başına birden çok hesap | Bu özellik, Microsoft hesabı ve Azure Active Directory gibi birden çok hizmet arasında aynı güvenlik anahtarını kullanmanıza da sağlar. |

Aşağıdaki sağlayıcılar, passwordless deneyimiyle uyumlu oldukları bilinen farklı form faktörlerinin FIDO2 güvenlik anahtarlarını sunmaktadır. Microsoft, müşterilerin ve FIDO Alliance ile iletişim kurarak bu anahtarların güvenlik özelliklerini değerlendirmesini önermektedir.

| Sağlayıcı | İletişim |
| --- | --- |
| Yılıco | [https://www.yubico.com/support/contact/](https://www.yubico.com/support/contact/) |
| Feian | [https://www.ftsafe.com/about/Contact_Us](https://www.ftsafe.com/about/Contact_Us) |
| CIHAZDAN | [https://www.hidglobal.com/contact-us](https://www.hidglobal.com/contact-us) |
| Ensurlik | [https://www.ensurity.com/contact](https://www.ensurity.com/contact) |
| eWBM | [https://www.ewbm.com/support](https://www.ewbm.com/support) |
| AuthenTrend | [https://authentrend.com/about-us/#pg-35-3](https://authentrend.com/about-us/#pg-35-3) |

> [!NOTE]
> Satınalma ve NFC tabanlı güvenlik anahtarları kullanmayı planlıyorsanız, desteklenen bir NFC okuyucusuna ihtiyacınız olacaktır.

Bir satıcısıysanız ve cihazınızı bu listede almak istiyorsanız [Fido2Request@Microsoft.com](mailto:Fido2Request@Microsoft.com)başvurun.

FIDO2 güvenlik anahtarları, çok güvenliğe duyarlı olan veya telefon numarası ikinci bir faktör olarak kullanabilecek senaryolar veya çalışanlar olan kuruluşlar için harika bir seçenektir.

## <a name="what-scenarios-work-with-the-preview"></a>Önizlemele hangi senaryolar çalışıyor?

- Yöneticiler, kiracı için passwordless kimlik doğrulama yöntemlerini etkinleştirebilir
- Yöneticiler tüm kullanıcıları hedefleyebilir veya kiracının her bir yöntemi için kullanıcıları/grupları seçebilir
- Son kullanıcılar, bu passwordless kimlik doğrulama yöntemlerini hesap portalında kaydedebilir ve yönetebilir
- Son kullanıcılar bu passwordless kimlik doğrulama yöntemleriyle oturum açabilirler
   - Microsoft Authenticator uygulama: tüm tarayıcılarda, Windows 10 kutudan çıkar (OOBE) kurulumu sırasında ve herhangi bir işletim sisteminde tümleşik mobil uygulamalarla birlikte Azure AD kimlik doğrulamasının kullanıldığı senaryolarda çalışacaktır.
   - Güvenlik anahtarları: Microsoft Edge gibi desteklenen tarayıcılarda Windows 10 ve Web için kilit ekranında çalışır.

## <a name="next-steps"></a>Sonraki adımlar

[Kuruluşunuzda FIDO2 güvenlik anahtarı passwordlesss seçeneklerini etkinleştirin](howto-authentication-passwordless-security-key.md)

[Kuruluşunuzda telefon tabanlı parolasız seçenekleri etkinleştirin](howto-authentication-passwordless-phone.md)

### <a name="external-links"></a>Dış bağlantılar

[FıDO Alliance](https://fidoalliance.org/)

[FIDO2 CTAP belirtimi](https://fidoalliance.org/specs/fido-v2.0-id-20180227/fido-client-to-authenticator-protocol-v2.0-id-20180227.html)
